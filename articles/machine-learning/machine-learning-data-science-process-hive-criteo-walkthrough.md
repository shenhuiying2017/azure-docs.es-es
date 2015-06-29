<properties 
	pageTitle="Proceso analítico avanzado y tecnología en acción: uso de clústeres de Hadoop de HDInsight en un conjunto de datos de 1 TB de Criteo | Microsoft Azure" 
	description="Uso del proceso analítico avanzado y tecnología (ADAPT) para un escenario integral con un clúster de Hadoop de HDInsight para crear e implementar un modelo con un conjunto de datos disponible públicamente de gran tamaño (1 TB)" 
	metaKeywords="" 
	services="machine-learning,hdinsight" 
	solutions="" 
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
	ms.date="05/27/2015" 
	ms.author="ginathan;mohabib;bradsev" />

# Proceso analítico avanzado y tecnología en acción: uso de clústeres de Hadoop de HDInsight de Azure en un conjunto de datos de 1 TB

En este tutorial, se describe cómo utilizar todo el proceso analítico avanzado y tecnología (ADAPT) con un [clúster de Hadoop de HDInsight de Azure](http://azure.microsoft.com/services/hdinsight/) para almacenar, explorar y estudiar sus características desde el punto de vista de los ingenieros y reducir el tamaño de los datos de uno de los conjuntos de datos de [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) que están disponibles públicamente. Usamos el Aprendizaje automático de Azure para crear modelos de regresión y clasificación binarios con estos datos. Asimismo, se muestra cómo publicar uno de estos modelos como un servicio web.

También es posible utilizar un bloc de notas de iPython para realizar las tareas que se presentan en este tutorial. Los usuarios que deseen probar este método deben consultar el tema sobre el [tutorial de Criteo con una conexión de ODBC de Hive](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).


## <a name="dataset"></a>Descripción del conjunto de datos de Criteo

Los datos de Criteo son un conjunto de datos de predicción de clics que ocupan aproximadamente 370 GB de archivos TSV comprimidos en gzip (1,3 TB aproximadamente sin comprimir). Constan de más de 4300 millones de registros. Estos datos proceden de 24 días de datos de clics que ofrece [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/). Para facilitar el trabajo de los científicos que estudian los datos, hemos descomprimido los datos disponibles para nosotros a fin de experimentar con ellos.

Cada registro de este conjunto de datos contiene 40 columnas:

- La primera columna es una columna de etiqueta que indica si un usuario hace clic en un anuncio (valor 1) o si no hace clic (valor 0). 
- Las 13 columnas siguientes son numéricas. 
- Las últimas 26 son columnas de categorías. 

Las columnas son anónimas y utilizan una serie de nombres enumerados: desde "Col1" (para la columna de etiqueta) hasta 'Col40 "(para la última columna de categoría).

Este es un extracto de las 20 primeras columnas de dos observaciones (filas) de este conjunto de datos:

	Col1	Col2	Col3	Col4	Col5	Col6	Col7	Col8	Col9	Col10	Col11	Col12	Col13	Col14	Col15			Col16			Col17			Col18			Col19		Col20	

	0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
	0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

Hay valores que faltan en las columnas numéricas y de categorías de este conjunto de datos. A continuación, se describe un método sencillo para administrar los valores que faltan. Más adelante, se describen detalles adicionales de los datos al almacenarlos en tablas de Hive.

**Definición:** *Porcentaje de clics (CTR, del inglés Click-through rate):* es el porcentaje de clics que se hacen en los datos. En este conjunto de datos de Criteo, el valor de CTR es aproximadamente del 3,3 % (o 0,033).

## <a name="mltasks"></a>Ejemplos de tareas de predicción
En este tutorial, se describen dos problemas de predicción de ejemplo:

1. **Clasificación binaria**: predice si un usuario ha hecho clic o no en un anuncio:
	- Clase 0: no hace clic.
	- Clase 1: sí hace clic.

2. **Regresión**: predice la probabilidad de que se haga clic en un anuncio en función de las características del usuario.


## <a name="setup"></a>Configuración de un clúster de Hadoop de HDInsight para la ciencia de los datos

**Nota:** esta tarea la suelen hacer los **administradores**.

Configure su entorno de ciencia de datos de Azure para crear soluciones de análisis predictivos con los clústeres de HDInsight en tres pasos:

1. [Cree una cuenta de almacenamiento](../storage-whatis-account.md): esta cuenta de almacenamiento se utiliza para almacenar datos en el almacenamiento de blobs de Azure. Los datos utilizados en los clústeres de HDInsight se almacenan aquí.

2. [Personalice los clústeres de Hadoop de HDInsight de Azure para la ciencia de los datos](machine-learning-data-science-customize-hadoop-cluster.md): en este paso, se crea un clúster de Hadoop de HDInsight de Azure con Anaconda Python 2.7 de 64 bits instalado en todos los nodos. Hay que llevar a cabo dos pasos importantes (descritos en este tema) para personalizar el clúster de HDInsight.

	* Hay que vincular la cuenta de almacenamiento que creó en el paso 1 con el clúster de HDInsight en el momento de su creación. Esta cuenta de almacenamiento se utiliza para tener acceso a datos que se pueden procesar en el clúster.

	* Debe habilitar el acceso remoto en el nodo principal del clúster después de crearlo. Recuerde las credenciales de acceso remoto que especifique aquí (distintas de las especificadas para el clúster en su creación), ya que las necesitará más adelante.

3. [Cree un área de trabajo de Aprendizaje automático (ML) de Azure](machine-learning-create-workspace.md): esta área de trabajo de Aprendizaje automático de Azure se usa para generar modelos de Aprendizaje automático después de una exploración de datos inicial y una reducción de su tamaño en el clúster de HDInsight.

## <a name="getdata"></a>Obtención y consumo de datos desde un origen público

Para acceder al conjunto de datos de [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/), haga clic en el vínculo, acepte las condiciones de uso y especifique un nombre. A continuación, se muestra una instantánea de esta pantalla:

![Aceptar los términos de Criteo](http://i.imgur.com/hLxfI2E.png)

Haga clic en **Continue to download** (Continuar para descarga) para obtener más información sobre el conjunto de datos y su disponibilidad.

Los datos residen en una ubicación pública de [almacenamiento de blobs de Azure](../storage-dotnet-how-to-use-blobs.md): wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. "wasb" hace referencia a la ubicación de almacenamiento de blobs de Azure.

1. Los datos de este almacenamiento de blobs públicos constan de tres subcarpetas de datos sin comprimir.
		
	1. La subcarpeta *raw/count/* contiene los primeros 21 días de datos, desde day_00 hasta day_20.
	2. La subcarpeta *raw/train/* consta de un único día de datos, day_21.
	3. La subcarpeta *raw/test/* consta de dos días de datos, day_22 y day_23.

2. Para aquellos que deseen comenzar con los datos gzip sin formato, podrán encontrar estos datos también en la carpeta principal *raw/*, con la nomenclatura "day_NN.gz", donde NN es un valor entre 00 y 23.

Más adelante en este tutorial, al hablar de la creación de tablas de Hive, se expone un enfoque alternativo para acceder a estos datos, explorarlos y modelarlos que no requiere ninguna descarga local.

## <a name="login"></a>Inicio de sesión en el nodo principal del clúster

Para iniciar sesión en el nodo principal del clúster, utilice el [Portal de administración de Azure](manage.windowsazure.com) para buscar el clúster. Haga clic en el icono del elefante de HDInsight a la izquierda y, a continuación, haga doble clic en el nombre del clúster. Navegue hasta la pestaña **Configuration** (Configuración), haga doble clic en el icono Connect (Conectar), situado en la parte inferior de la página, y escriba las credenciales de acceso remoto cuando se le soliciten. Así accederá al nodo principal del clúster.

Esto es lo que se ve normalmente al iniciar sesión por primera vez en el nodo principal del clúster:

![Iniciar sesión en el clúster](http://i.imgur.com/Yys9Vvm.png)

A la izquierda, vemos el icono de "Hadoop Command Line" (Línea de comandos de Hadoop), que será nuestra herramienta de trabajo para explorar los datos. También vemos dos direcciones URL útiles: "Hadoop Yarn Status" (Estado de Hadoop Yarn) y "Hadoop Name Node" (Nombre del nodo de Hadoop). La dirección URL del estado de Yarn muestra el progreso del trabajo, mientras que la URL del nombre del nodo proporciona detalles sobre la configuración del clúster.

Ya contamos con la configuración adecuada y estamos listos para comenzar la primera parte del tutorial, que es la exploración de datos mediante Hive y la preparación de estos para el Aprendizaje automático de Azure.

## <a name="hive-db-tables"></a> Creación de tablas y base de datos de Hive

Para crear tablas de Hive para nuestro conjunto de datos de Criteo, haga clic en el icono ***Hadoop Command Line*** (Línea de comandos de Hadoop) en el escritorio del nodo principal y especifique el directorio de Hive mediante este comando:

    cd %hive_home%\bin

**Nota importante**: **ejecute todos los comandos de Hive que aparecen en este tutorial desde el símbolo del sistema del directorio bin/ de Hive que aparece anteriormente. De esta manera, cualquier problema con la ruta de acceso se solucionará automáticamente. Utilizaremos indistintamente los términos "símbolo del sistema del directorio de Hive", "símbolo del sistema del directorio bin/ de Hive" y "línea de comandos de Hadoop".**

**Nota importante 2**: **para ejecutar cualquier consulta de Hive, siempre se puede hacer lo siguiente** cd %hive_home%\bin hive

Después de que aparezca Hive REPL con un signo "hive >", solo tendrá que cortar y pegar la consulta para ejecutarla.

El código siguiente crea una base de datos "criteo" y, a continuación, genera 4 tablas:


* Una *tabla count*, que incluye los días desde day_00 hasta day_20. 
* Una *tabla train*, que incluye day_21. 
* Dos *tablas test* correspondientes a day_22 y day_23, respectivamente. 

Dividimos nuestro conjunto de datos de prueba en dos tablas distintas porque uno de los días es festivo y queremos determinar si el modelo puede detectar las diferencias entre un día festivo y uno laborable a partir del porcentaje de clics.

Para mayor comodidad, el script [sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) se muestra a continuación:

	CREATE DATABASE IF NOT EXISTS criteo;
	DROP TABLE IF EXISTS criteo.criteo_count;
	CREATE TABLE criteo.criteo_count (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

	DROP TABLE IF EXISTS criteo.criteo_train;
	CREATE TABLE criteo.criteo_train (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

	DROP TABLE IF EXISTS criteo.criteo_test_day_22;
	CREATE TABLE criteo.criteo_test_day_22 (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

	DROP TABLE IF EXISTS criteo.criteo_test_day_23;
	CREATE TABLE criteo.criteo_test_day_23 (
	col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
	ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
	LINES TERMINATED BY '\n'
	STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

Observamos que todas estas tablas son externas ya que simplemente señalan a ubicaciones de almacenamiento de blobs de Azure (wasb).

**Hay dos maneras de ejecutar todas las consultas de Hive mencionadas.**

1. **Usar la línea de comandos de REPL de Hive**: el primer método consiste en emitir un comando "hive" y, a continuación, copiar la consulta anterior y pegarla en la línea de comandos de REPL de Hive. Para ello, ejecute lo siguiente:

		cd %hive_home%\bin
		hive

 	Ahora, en la línea de comandos de REPL, la consulta se ejecuta al cortarla y pegarla.

2. **Guardar las consultas en un archivo y ejecutar el comando**: el segundo método consiste en guardar las consultas en un archivo .hql ([sample&#95;hive&#95;create&#95;criteo&#95;database&#95;and&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) y, a continuación, emitir el siguiente comando para ejecutar la consulta:

		hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql


### Confirmación de la creación de la tabla y la base de datos

A continuación, confirmamos la creación de la base de datos emitiendo el comando siguiente desde el símbolo del sistema del directorio bin/ de Hive:

		hive -e "show databases;"

Este es el resultado:

		criteo
		default
		Time taken: 1.25 seconds, Fetched: 2 row(s)

Esto confirma la creación de la nueva base de datos, "criteo".

Para ver qué tablas hemos creado, simplemente emitimos el comando siguiente desde el símbolo del sistema del directorio bin/ de Hive:

		hive -e "show tables in criteo;"

Se mostrará el siguiente resultado:

		criteo_count
		criteo_test_day_22
		criteo_test_day_23
		criteo_train
		Time taken: 1.437 seconds, Fetched: 4 row(s)

##<a name="exploration"></a> Exploración de datos en Hive

Ahora estamos preparados hacer algunas exploraciones de datos básicas en Hive. Comenzamos contando el número de ejemplos de las tablas de datos "train" y "test".

### Número de ejemplos de "train"

El contenido de [sample&#95;hive&#95;count&#95;train&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) se muestra a continuación:

		SELECT COUNT(*) FROM criteo.criteo_train;

El resultado es:

		192215183
		Time taken: 264.154 seconds, Fetched: 1 row(s)

Como alternativa, también se puede emitir el comando siguiente desde el símbolo del sistema del directorio bin/ de Hive:

		hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### Número de ejemplos de "test" en los dos conjuntos de datos

Ahora vamos a contar el número de ejemplos que hay en los dos conjuntos de datos "test". El contenido de [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;22&#95;table&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) es el siguiente:

		SELECT COUNT(*) FROM criteo.criteo_test_day_22;

El resultado es:
	
		189747893
		Time taken: 267.968 seconds, Fetched: 1 row(s)

Como es habitual, también podemos llamar al script desde el símbolo del sistema del directorio bin/ de Hive emitiendo el comando siguiente:

		hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Por último, examinamos el número de ejemplos de "test" que hay en el conjunto de datos basados en day_23.

El comando para hacer esto es similar a la anterior (consulte [sample&#95;hive&#95;count&#95;criteo&#95;test&#95;day&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

		SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Este es el resultado:
	
		178274637
		Time taken: 253.089 seconds, Fetched: 1 row(s)

### Distribución de etiquetas en el conjunto de datos "train"

La distribución de etiquetas del conjunto de datos "train" es interesante. Para verlo, mostramos el contenido de [sample&#95;hive&#95;criteo&#95;label&#95;distribution&#95;train&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

		SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

De esta forma, obtenemos la distribución de etiquetas:

		1       6292903
		0       185922280
		Time taken: 459.435 seconds, Fetched: 2 row(s)

Tenga en cuenta que el porcentaje de las etiquetas positivas es del 3,3 % (coherente con el conjunto de datos original).
		
### Distribuciones del histograma de algunas variables numéricas en el conjunto de datos "train"

Podemos usar la función nativa de Hive "histogram_numeric" para conocer la distribución de las variables numéricas. El contenido de [sample&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql) es el siguiente:

		SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM 
			(SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
			) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

El resultado es este:

		26      155878415
		2606    92753
		6755    22086
		11202   6922
		14432   4163
		17815   2488
		21072   1901
		24113   1283
		27429   1225
		30818   906
		34512   723
		38026   387
		41007   290
		43417   312
		45797   571
		49819   428
		53505   328
		56853   527
		61004   160
		65510   3446
		Time taken: 317.851 seconds, Fetched: 20 row(s)

La combinación "LATERAL VIEW - explode" en Hive sirve para producir un resultado similar a SQL en lugar de la lista habitual. Tenga en cuenta que, en la tabla anterior, la primera columna corresponde al centro de bin y la segunda, a la frecuencia de bin.

### Percentiles aproximados de algunas de las variables numéricas del conjunto de datos "train"

Con respecto a las variables numéricas, también es interesante el cálculo de los percentiles aproximados. La función nativa de Hive "percentile_approx" permite realizar esta acción. El contenido de [sample&#95;hive&#95;criteo&#95;approximate&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) es el siguiente:

		SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

El resultado es:

		1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
		Time taken: 564.953 seconds, Fetched: 1 row(s)

Observamos que la distribución de los percentiles suele estar estrechamente relacionada con la distribución de histograma de cualquier variable numérica.

### Búsqueda del número de valores únicos para algunas columnas de categorías en el conjunto de datos "train"

Continuamos con la exploración de datos y ahora vamos a buscar el número de valores únicos que adoptan algunas columnas de categorías. Para ello, mostramos el contenido de [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

		SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

El resultado es:

		19011825
		Time taken: 448.116 seconds, Fetched: 1 row(s)

Tenga en cuenta que Col15 tiene 19 millones de valores únicos. Usar técnicas simples como la codificación "one-hot" para codificar estas variables de categorías tan altamente dimensionales no es viable. En particular, vamos a explicar y mostrar una técnica eficaz y contundente llamada [Aprendizaje con recuentos](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) para hacer frente a este problema de forma eficaz.

Terminamos esta subsección examinando también el número de valores únicos de algunas otras columnas de categorías. El contenido de [sample&#95;hive&#95;criteo&#95;unique&#95;values&#95;multiple&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) es el siguiente:

		SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)), 
		COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
		FROM criteo.criteo_train;

El resultado es:

		30935   15200   7349    20067   3
		Time taken: 1933.883 seconds, Fetched: 1 row(s)

De nuevo vemos que, excepto Col20, todas las demás columnas tienen muchos valores únicos.

### Recuentos de aparición conjunta de pares de variables de categorías en el conjunto de datos "train"

Los recuentos de aparición conjunta de pares de variables de categorías son también interesantes. Esto se puede determinar mediante el código de [sample&#95;hive&#95;criteo&#95;paired&#95;categorical&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

		SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Hemos invertido el orden de los recuentos en función de su aparición y mostramos los 15 primeros en este caso. El resultado obtenido es el siguiente:

		ad98e872        cea68cd3        8964458
		ad98e872        3dbb483e        8444762
		ad98e872        43ced263        3082503
		ad98e872        420acc05        2694489
		ad98e872        ac4c5591        2559535
		ad98e872        fb1e95da        2227216
		ad98e872        8af1edc8        1794955
		ad98e872        e56937ee        1643550
		ad98e872        d1fade1c        1348719
		ad98e872        977b4431        1115528
		e5f3fd8d        a15d1051        959252
		ad98e872        dd86c04a        872975
		349b3fec        a52ef97d        821062
		e5f3fd8d        a0aaffa6        792250
		265366bf        6f5c7c41        782142
		Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a> Reducción del tamaño de los conjuntos de datos para el Aprendizaje automático de Azure

Una vez que hemos explorado los conjuntos de datos y que hemos mostrado cómo se hace este tipo de exploración sobre cualquier variable (incluidas las combinaciones), ahora vamos a reducir el tamaño de los conjuntos de datos para que se puedan crear modelos en el Aprendizaje automático de Azure. Recuerde que el problema en el que nos centramos es el siguiente: considerando un conjunto de atributos de ejemplo (valores de las características desde Col2 hasta Col40), vamos a predecir si Col1 tendrá un valor 0 (no se hace clic) o un valor 1 (sí se hace clic).

Para reducir el tamaño de los conjuntos de datos "train" y "test" al 1 % del tamaño original, utilizamos la función RAND() nativa de Hive. El siguiente script, [sample&#95;hive&#95;criteo&#95;downsample&#95;train&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql), nos permite hacerlo con el conjunto de datos "train":

		CREATE TABLE criteo.criteo_train_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		---Now downsample and store in this table

		INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

El resultado es:

		Time taken: 12.22 seconds
		Time taken: 298.98 seconds

El script [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) permite hacerlo con los datos de "test", en concreto, en "day_22":

		--- Now for test data (day_22)

		CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

El resultado es:

		Time taken: 1.22 seconds
		Time taken: 317.66 seconds


Por último, el script [sample&#95;hive&#95;criteo&#95;downsample&#95;test&#95;day&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) permite hacerlo con los datos de "test", en concreto, en "day_23":

		--- Finally test data day_23
		CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
		col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
		LINES TERMINATED BY '\n'
		STORED AS TEXTFILE;

		INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

El resultado es:

		Time taken: 1.86 seconds
		Time taken: 300.02 seconds

De esta forma, estamos listos para usar nuestros conjuntos de datos "train" y "test" con el tamaño reducido para crear modelos en el Aprendizaje automático de Azure.

Hay un componente importante final antes de pasar al Aprendizaje automático de Azure, que es la preocupación con la tabla de recuento. En la siguiente subsección, se trata este tema con más detalle.

##<a name="count"></a> Breve explicación sobre la tabla de recuento

Como hemos visto, algunas variables de categorías tienen una dimensionalidad muy alta. En nuestro tutorial, presentamos una técnica eficaz denominada [Aprendizaje con recuentos](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) para codificar estas variables de una manera eficiente y robusta. Para obtener más información sobre esta técnica, acceda al vínculo proporcionado.

**Nota:** en este tutorial, nos centramos en el uso de las tablas de recuento para producir representaciones compactas de características de categorías con una alta dimensionalidad. Sin duda, esta no es la única manera de codificar características de las categorías. Para obtener más información sobre otras técnicas, los usuarios interesados pueden consultar la información sobre la [codificación "one-hot"](http://en.wikipedia.org/wiki/One-hot) y la [aplicación de hash a las características](http://en.wikipedia.org/wiki/Feature_hashing).

Para crear tablas de recuento en los datos de recuento, se utilizan los datos de la carpeta raw/count. En la sección de modelado, mostramos a los usuarios cómo crear estas tablas de recuento para características de categorías desde cero, así como a utilizar una tabla de recuento pregenerada para sus exploraciones. En lo sucesivo, cuando nos referimos a las "tablas de recuento pregeneradas", nos referimos a usar las tablas de recuento proporcionadas. A continuación, encontrará instrucciones detalladas sobre cómo obtener acceso a estas tablas.

## <a name="aml"></a> Creación de modelos con el Aprendizaje automático de Azure

Nuestro modelo del proceso de creación con el Aprendizaje automático de Azure consta de estos pasos:

1. [Obtención de los datos a partir de las tablas de Hive para el Aprendizaje automático de Azure](#step1)
2. [Creación del experimento: limpieza de los datos, elección de un aprendiz y caracterización de las tablas de recuento](#step2)
3. [Entrenamiento del modelo](#step3)
4. [Puntuación del modelo con los datos de prueba](#step4)
5. [Evaluación del modelo](#step5)
6. [Publicación del modelo como un servicio web para su consumo](#step6)

Ahora estamos preparados para generar modelos en Estudio de aprendizaje automático de Azure. Nuestros datos con tamaño reducido están guardados como tablas de Hive en el clúster. Usaremos el módulo Reader (Lector) de Aprendizaje automático de Azure para leer estos datos. Las credenciales para acceder a la cuenta de almacenamiento de este clúster están a continuación.

### <a name="step1"></a> Paso 1: obtención de datos a partir de las tablas de Hive para el Aprendizaje automático de Azure con el módulo Reader (Lector) y selección para un experimento de Aprendizaje automático

Para el módulo **Reader** (Lector), los valores de los parámetros que se establecen en el gráfico son solo ejemplos. Estas son algunas orientaciones generales sobre cómo "rellenar" el conjunto de parámetros para el módulo **Lector**.

1. Elija "Hive Query" (Consulta de Hive) como origen de datos.
2. En "Hive Query" (Consulta de Hive), basta con seleccionar SELECT * FROM <nombre_base_datos.nombre_tabla>.
3. Hcatalog server URI (URI del servidor de Hcatalog): si el clúster es "abc", este valor será: https://abc.azurehdinsight.net
4. Hadoop user account name (Nombre de la cuenta de usuario de Hadoop): el nombre de usuario elegido en el momento de dar de alta el clúster (no es el nombre de usuario de acceso remoto).
5. Hadoop user account password (Contraseña de la cuenta de usuario de Hadoop): la contraseña de usuario elegida en el momento de dar de alta el clúster (no es la contraseña de acceso remoto).
6. Location of output data (Ubicación de los datos de salida): elija "Azure".
7. Azure storage account name (Nombre de la cuenta de almacenamiento de Azure): la cuenta de almacenamiento asociada al clúster.
8. Azure storage account key (Clave de la cuenta de almacenamiento de Azure): la clave de almacenamiento asociada al clúster.
9. Azure container name (Nombre del contenedor de Azure): si el nombre de clúster es "abc", este campo suele ser simplemente "abc". 

Este es el aspecto del módulo **Lector** al obtener los datos de la tabla de Hive:

![Obtener los datos del lector](http://i.imgur.com/i3zRaoj.png)

Una vez que el módulo **Lector** finaliza la obtención de datos (se muestra una marca de verificación verde en el módulo), guarde estos datos como un conjunto de datos (con el nombre que desee). Este es el aspecto:

![Guardar los datos del lector](http://i.imgur.com/oxM73Np.png)


Haga clic con el botón derecho en el puerto de salida del módulo **Lector**. Se muestran las opciones **Save as dataset** (Guardar como conjunto de datos) y **Visualize** (Visualizar). Si se hace clic en la opción **Visualize** (Visualizar), se muestran 100 filas de los datos, junto con un panel derecho que es útil para algunas estadísticas de resumen. Para guardar los datos, simplemente seleccione **Save as dataset** (Guardar como conjunto de datos) y siga las instrucciones.

Para seleccionar el conjunto de datos guardado para usarlo en un experimento de Aprendizaje automático, busque los conjuntos de datos utilizando la **búsqueda** que se muestra a continuación. A continuación, escriba parcialmente el nombre del conjunto de datos para acceder a él y arrastre el conjunto de datos hasta el panel principal. Al depositarlo en el panel principal, se selecciona para su uso en el modelado de Aprendizaje automático.

![Buscar el conjunto de datos](http://i.imgur.com/rx4nnUH.png)

Realice esta acción para los conjuntos de datos "test" y "train".

### <a name="step2"></a> Paso 2: creación de un experimento sencillo en el estudio de Aprendizaje automático de Azure para predecir los clics y los "no clics"

En primer lugar, mostramos una arquitectura de experimento sencillo. Después, profundizaremos un poco en los detalles. Primero, limpiaremos los datos. A continuación, elegiremos un aprendiz y, por último, mostraremos cómo caracterizar las tablas de recuento pregeneradas o creadas desde cero por el usuario.

![Experimentar con la arquitectura](http://i.imgur.com/R4iTLYi.png)

Para profundizar en los detalles, comenzaremos con los conjuntos de datos guardados, tal y como se muestra.

El módulo **Limpiar datos que faltan** hace justo lo que dice su nombre: limpia los datos siguiendo el método que especifique el usuario. En este módulo, veremos estos datos:

![Limpiar datos que faltan (Limpiar datos que faltan)](http://i.imgur.com/0ycXod6.png)

Aquí, hemos optado por reemplazar todos los valores que faltan por un 0. Hay otras opciones, que se pueden ver al mirar las listas desplegables del módulo.

A continuación, tenemos que elegir el aprendiz. Vamos a usar como nuestro aprendiz un árbol de decisiones incrementado de dos clases. En concreto, mostraremos cómo usar características de recuento en características de categorías altamente dimensionales para crear representaciones compactas de nuestro modelo y también para lograr entrenamiento (train) y unas pruebas (train) eficientes.

Hacemos aquí un inciso para explicar lo que son realmente las tablas de recuento: se trata de recuentos condicional de clase (en ocasiones, también nos referimos a ellas simplemente como "recuentos condicionales"). En esencia, es una manera de contar los valores de una característica para cada clase y usar estos recuentos para calcular las probabilidades.


#### Obtención de acceso a las tablas de recuento pregeneradas para el modelado

Para obtener acceso a nuestras tablas de recuento pregeneradas, haga clic en **Gallery** (Galería), como se muestra a continuación:

![Gallery (Galería)](http://i.imgur.com/TsWkig3.png)

Al hacer clic en **Gallery** (Galería), accedemos a una página similar a la siguiente:

![Página principal de Gallery (Galería)](http://i.imgur.com/dmXo0KR.png)

A continuación, busque la frase "recuentos de criteo" (criteo counts) y desplácese hacia abajo por la lista de resultados. Debería ver lo siguiente:

![Recuentos de Criteo](http://i.imgur.com/JZ119Jf.png)

Al hacer clic en este experimento, accedemos a una página similar a la siguiente:

![Recuentos](http://i.imgur.com/dxdjMjh.png)

A continuación, haga clic en **Open in Studio** (Abrir en estudio) para copiar el experimento en el área de trabajo. Esta acción también copia automáticamente los conjuntos de datos; en este caso, los dos conjuntos de datos claves de interés son la tabla de recuento y los metadatos de recuento, que describimos con más detalle.

#### Características de recuento en el conjunto de datos

Los módulos siguientes de nuestro experimento implican el uso de tablas de recuento pregeneradas. Para usar estas tablas de recuento pregeneradas, busque "cr_count_" en la pestaña de búsqueda de un experimento nuevo. Se mostrarán dos conjuntos de datos: "cr_count_cleanednulls_metadata" y "cr_count_table_cleanednulls". Arrastre estos elementos y colóquelos en el panel del experimento de la derecha. Al hacer clic con el botón derecho en los puertos de salida, como siempre, podemos ver su aspecto.

Los metadatos de la tabla de recuento tienen este aspecto:

![Metadatos de la tabla de recuento](http://i.imgur.com/A39PIe7.png)

Tenga en cuenta que los metadatos tienen información sobre las columnas en las que se basan los recuentos condicionales, si se ha usado un diccionario para generarlas (una alternativa es el resumen de recuento mínimo), el valor de hash utilizado, el número de bits de hash utilizado para aplicar hash a la características, el número de clases, etc.

La tabla de recuento tiene este aspecto:

![Tabla de recuento](http://i.imgur.com/NJn1EQO.png)

Podemos ver que la tabla de recuento tiene información sobre los recuentos condicionales de clase. El valor de las características de la categorías está en "Hash Value" (Valor hash), por lo que las propias características tienen aplicado un algoritmo hash.

¿Cómo se integran las características de recuento en los conjuntos de datos? Para ello, utilizamos el módulo **Caracterizador** del recuento, que se muestra a continuación:

![Módulo Caracterizador de recuento](http://i.imgur.com/dnMdrR1.png)

Una vez que se crea la tabla de recuento (recuerde que aquí estamos creando recuentos condicionales de clase de características de categorías), usamos el módulo **Caracterizador** mostrado anteriormente para crear estas características de recuento en nuestro conjunto de datos. Como podemos ver, el módulo **Caracterizador** permite elegir qué características se deben tener en cuenta, si necesitamos simplemente las probabilidades de registro o también los recuentos y otras opciones avanzadas.

#### Creación una tabla de recuento desde cero

Recuerde que mencionamos en "Breve explicación sobre la tabla de recuento" que, además de utilizar las tablas de recuento pregeneradas (que tratamos en detalle en secciones anteriores), los usuarios también pueden crear sus propias tablas de recuento desde cero.

En esta sección, se muestra cómo crear una tabla de recuento desde cero. Para ello, utilizamos el módulo **Crear tabla de recuento** que se muestra a continuación con sus valores:

![Módulo Crear tabla de recuento](http://i.imgur.com/r7pP8Qq.png)

A continuación, aparece la última parte de la configuración de este módulo:

![Configurar el módulo Crear tabla de recuento](http://i.imgur.com/PvmSh3C.png)


**Nota importante:** para configurar la cuenta de almacenamiento y el clúster, utilice los valores pertinentes.

Al hacer clic en **Run** (Ejecutar), podemos crear las tablas de recuento en el clúster seleccionado. El resultado es, como se muestra anteriormente, la tabla de recuento y sus metadatos asociados. Con estas tablas listas, ahora podemos crear el experimento.


### <a name="step3"></a> Paso 3: entrenamiento del modelo

Para seleccionar esta opción, escriba "two class boosted" (dos clases incrementado) en el cuadro de búsqueda y arrastre el módulo aquí. Utilizamos los valores predeterminados del aprendiz del árbol de decisiones incrementado, que se muestra a continuación:

![Aprendiz de BDT](http://i.imgur.com/dDk0Jtv.png)

Necesitamos tres componentes finales antes de ejecutar el experimento de Aprendizaje automático.

El primero es un módulo Entrenar modelo: aunque el primer puerto considera al aprendiz como entrada, el segundo puerto adopta el conjunto de datos "train" para aprender. Vemos el aspecto actual debajo y, a continuación, mostramos qué parámetros hay que configurar en el módulo.

![Conexiones con el módulo Entrenar modelo de BDT](http://i.imgur.com/szS2xBb.png)

![Configuración del módulo Evaluar modelo de BDT](http://i.imgur.com/nd7lHBL.png)

### <a name="step4"></a> Paso 4: puntuación del modelo con un conjunto de datos de "test"

El segundo componente ofrece una forma de puntuar el conjunto de datos de "test". Esto se hace cómodamente mediante el módulo **Puntuar modelo**: se considera como entrada el modelo aprendido a partir del conjunto de datos "train". El conjunto de datos "test" se usa para hacer predicciones. Este es el aspecto que tiene.

![Conexiones del modelo de puntuación de BDT](http://i.imgur.com/AwIH1rH.png)

### <a name="step5"></a> Paso 5: evaluación del modelo

Por último, vamos a ver el rendimiento del modelo. Normalmente, para los problemas de clasificación (binarios) de dos clases, una buena medida es AUC. Para visualizar esto, conectamos el módulo "Puntuar modelo" con un módulo "Evaluar modelo". Al hacer clic en **Visualize** (Visualizar) en el módulo **Evaluar modelo**, se genera un gráfico como el siguiente:

![Módulo Evaluación del modelo de BDT](http://i.imgur.com/0Tl0cdg.png)

En los problemas de clasificación binarios (o de dos clases), una buena medida de la exactitud de la predicción es AUC. A continuación, mostramos nuestros resultados al usar este modelo en nuestro conjunto de datos "test". Para ver los resultados, haga clic con el botón derecho en el puerto del módulo **Evaluar modelo** y seleccione **Visualize** (Visualizar).

![Visualización del módulo Evaluar modelo](http://i.imgur.com/IRfc7fH.png)

### <a name="step6"></a> Paso 6: publicación del modelo como un servicio web
Es muy interesante la posibilidad de publicar los modelos de Aprendizaje automático como servicios web. Una vez hecho esto, podemos hacer llamadas al servicio web utilizando los datos para los que necesitamos predicciones. Lo ideal es que el modelo devuelva una predicción de algún tipo.

Para ello, primero guardamos el modelo con el que hemos entrenado como un objeto del Modelo entrenado. Para ello, haga clic con el botón derecho en el módulo **Entrenar modelo** y utilice la opción "Save as Trained Model" (Guardar como modelo entrenado).

A continuación, deseamos crear un puerto de entrada y salida para el servicio web: un puerto de entrada que considere los datos de la misma forma que los datos para los que necesitamos predicciones; y un puerto de salida que devuelva las etiquetas de puntuación y las probabilidades asociadas.

#### Selección de algunas filas de datos para el puerto de entrada

Ahora, mostramos cómo seleccionar algunas filas de datos para el puerto de entrada.

![Datos del puerto de entrada](http://i.imgur.com/XqVtSxu.png)

Cómodamente podemos utilizar una transformación de aplicación de SQL para seleccionar simplemente 10 filas que sirvan como los datos del puerto de entrada.

#### Servicio web
Ahora estamos preparados para realizar un pequeño experimento que puede utilizarse para publicar el servicio web.

#### Generación de datos de entrada para el servicio web

Como paso inicial, ya que la tabla de recuento es grande, tomamos unas pocas líneas de datos de prueba y generamos datos de salida a partir de ellos con características de recuento. Esto sirve como el formato de datos de entrada para nuestro servicio web. Vea la siguiente imagen:

![Creación de datos de entrada de BDT](http://i.imgur.com/OEJMmst.png)

Nota: para el formato de datos de entrada, utilizaremos ahora la salida del módulo **Caracterizador de recuento**. Una vez que este experimento termina de ejecutarse, guarde la salida del módulo **Caracterizador de recuento** como un conjunto de datos. **Nota importante:** este conjunto de datos se usará para los datos de entrada en el servicio web.

#### Puntuación del experimento para la publicación del servicio web

En primer lugar, veamos el aspecto que tiene. La estructura fundamental es un módulo Puntuar modelo que acepta el objeto del modelo entrenado y unas pocas líneas de datos de entrada que hemos generado en los pasos anteriores mediante el módulo **Caracterizador de recuento**. Utilizamos "Columnas del proyecto" para proyectar las etiquetas puntuadas y las probabilidades de puntuación.

![Columnas del proyecto](http://i.imgur.com/kRHrIbe.png)

Resulta instructivo ver cómo se puede utilizar el módulo Columnas del proyecto para "filtrar" los datos de un conjunto de datos. El contenido se muestra a continuación:

![Filtrar con el módulo Columnas del proyecto](http://i.imgur.com/oVUJC9K.png)

Para obtener los puertos de salida y entrada azules, basta con hacer clic en "Prepare Webservice" (Preparar servicio web) en la esquina inferior derecha. Al realizar este experimento también podemos publicar el servicio web haciendo clic en el icono **Publish webservice** (Publicar servicio web) situado en la esquina inferior derecha, como se muestra a continuación.

![Publicar servicio web](http://i.imgur.com/WO0nens.png)

Una vez publicado el servicio web, accedemos a una página como esta:

![](http://i.imgur.com/YKzxAA5.png)

Podemos ver los dos vínculos a los servicios web en el lado izquierdo:

* El servicio **Request/Response** (Solicitud/respuesta) (o RRS) está destinados a predicciones únicas y es lo que vamos a utilizar en este taller. 
* El servicio **Batch Execution** (Ejecución por lotes) (o BES), como su nombre implica, se utiliza para las predicciones por lotes y requiere que los datos sobre los que se van a realizar las predicciones residan en un blob de Azure.

Al hacer clic en el vínculo **Request/Response** (Solicitud/respuesta), accedemos a una página que nos da un código predefinido en C#, python y R. Este código puede utilizarse fácilmente para realizar llamadas al servicio web. Tenga en cuenta que hay que utilizar la clave de API en esta página para la autenticación.

Se aconseja copiar este código python en una celda nueva en el bloc de notas de iPython.

A continuación, se muestra un fragmento de código python con la clave de API correcta.

![Código de Python](http://i.imgur.com/f8N4L4g.png)

Tenga en cuenta que hemos reemplazado la clave de API predeterminada por la clave de API de nuestros servicios web. Al hacer clic en "Run" (Ejecutar) en esta celda en bloc de notas de iPython, se produce la respuesta siguiente:

![Respuesta de iPython](http://i.imgur.com/KSxmia2.png)

Podemos ver que para los dos ejemplos de prueba por los que hemos preguntado (en el marco JSON del script de python), obtenemos respuestas con el formato "Scored Labels, Scored Probabilities" (Etiquetas puntuadas, Probabilidades puntuadas). Tenga en cuenta que, en este caso, elegimos los valores predeterminados que proporciona el código predefinido (0 para todas las columnas numéricas y la cadena "value" para todas las columnas de categorías).

Con esto concluye la explicación sobre cómo administrar conjuntos de datos a gran escala de forma integral con Aprendizaje automático de Azure: hemos pasado directamente desde un terabyte de datos hasta un modelo de predicción que se implementa como un servicio web en la nube.

 

<!---HONumber=58_postMigration-->