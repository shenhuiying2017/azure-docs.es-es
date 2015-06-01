<properties 
	pageTitle="Enviar consultas de subárbol a clústeres de Hadoop de HDInsight en el proceso de ciencia de datos en la nube | Azure" 
	description="Procesar datos de tablas de subárbol" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="hangzh-msft" 
	manager="jacob.spoelstra" 
	editor="cgronlun"  />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="hangzh;bradsev" /> 

#<a name="heading"></a> Enviar consultas de subárbol a clústeres de Hadoop de HDInsight en el proceso de ciencia de datos en la nube

En este documento se describen distintas formas de enviar consultas de subárbol a los clústeres de Hadoop administrados por un servicio HDInsight de Azure. Las consultas de subárbol se pueden enviar mediante: 

* la línea de comandos de Hadoop en el nodo principal del clúster
* el Bloc de notas de IPython 
* el Editor de subárbol
* Scripts de PowerShell de Azure 

Se proporcionan consultas de subárbol genéricas que pueden usarse para explorar los datos o para generar funciones que usan funciones definidas por el usuario de subárbol integradas (UDF). 

También se proporcionan ejemplos de consultas específicas de escenarios de [Datos de viajes en taxi en Nueva York](http://chriswhong.com/open-data/foil_nyc_taxi/) en [Repositorio de Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Estas consultas ya tienen el esquema de datos especificado y están listas para enviarse para su ejecución. 

En la última sección, se describen los parámetros que los usuarios pueden ajustar para que se pueda mejorar el rendimiento de las consultas de subárbol.

## Requisitos previos
En este artículo se supone que ha:
 
* creado una cuenta de almacenamiento de Azure. Si necesita instrucciones, vea [Creación de una cuenta de almacenamiento de Azure](hdinsight-get-started.md#storage) 
* aprovisionado un clúster de Hadoop con el servicio HDInsight.  Si necesita instrucciones, consulte [Aprovisionamiento de un clúster de HDInsight](hdinsight-get-started.md#provision).
* Se han cargado los datos en tablas de subárbol en clústeres de Hadoop de HDInsight de Azure. De no ser así, siga [Crear y cargar datos en tablas de subárbol](machine-learning-data-science-hive-tables.md)  para cargar los datos en tablas de subárbol primero.
* habilitado el acceso remoto al clúster. Si necesita instrucciones, vea [Acceder al nodo principal del clúster de Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#remoteaccess). 


- [Cómo enviar consultas de subárbol](#submit)
- [Exploración de datos e ingeniería de funciones](#explore)
- [Temas avanzados: Ajustar los parámetros de subárbol para mejorar la velocidad de consulta](#tuning)

## <a name="submit"></a>Cómo enviar consultas de subárbol

###1. A través de la línea de comandos de Hadoop en el nodo principal del clúster de Hadoop

Si la consulta es compleja, enviar consultas de subárbol directamente en el nodo principal del clúster de Hadoop normalmente permite obtener respuestas más rápidas que si se efectúa el envío mediante un editor de subárboles o scripts de Azure PowerShell. 

Inicie sesión en el nodo principal del clúster de Hadoop, abra la línea de comandos de Hadoop en el escritorio del nodo principal y escriba el comando `cd %hive_home%\bin`.

Los usuarios disponen de tres maneras de enviar consultas de subárbol en la línea de comandos de Hadoop. 

####Envíe consultas de subárbol directamente en la línea de comandos de Hadoop. 

Los usuarios pueden ejecutar el comando como `hive -e "<su consulta de subárbol>;` para enviar consultas de subárbol sencillas directamente en la línea de comandos de Hadoop. Este es un ejemplo en el que el cuadro rojo destaca el comando que envía la consulta de subárbol y el cuadro verde destaca el resultado de la consulta de subárbol.

![Create workspace][10]

####Enviar consultas de subárbol en archivos .hql

Cuando la consulta de subárbol es más complicada y tiene varias líneas, no resulta práctico modificar consultas en la línea de comandos o la consola de comandos de subárbol. Una alternativa es usar un editor de texto en el nodo principal del clúster de Hadoop y guardar las consultas de subárbol en un archivo .hql de un directorio local del nodo principal. A continuación, la consulta de subárbol del archivo .hql puede enviarse mediante el argumento `-f` del modo indicado a continuación:
	
	`hive -f "<path to the .hql file>"`

![Create workspace][15]


####Suprimir la impresión de pantalla del estado de progreso de las consultas de subárbol

De forma predeterminada, después de enviar la consulta de subárbol en la línea de comandos de Hadoop, el progreso del trabajo de asignación/reducción se imprimirá en pantalla. Para suprimir la impresión de la pantalla del progreso del trabajo de asignación/reducción, puede utilizar un argumento `-S` ("S" en mayúsculas) en la línea de comandos del modo indicado a continuación:

	hive -S -f "<path to the .hql file>"
	hive -S -e "<Hive queries>"

####Envíe consultas de subárbol en la consola de comandos de subárbol.

Los usuarios también pueden especificar en primer lugar la consola de comandos Hive ejecutando el comando `hive` en línea de comandos de Hadoop y, a continuación, enviar consultas de subárbol en la consola de comandos de subárbol. Aquí tiene un ejemplo: En este ejemplo, los dos cuadros de color rojo resaltan los comandos que se utilizan para escribir en la consola de comandos de subárbol y la consulta de subárbol enviada en la consola de comandos de subárbol, respectivamente. El cuadro verde resalta el resultado de la consulta de subárbol. 

![Create workspace][11]

Los ejemplos anteriores generan directamente los resultados de la consulta de subárbol en pantalla. Los usuarios también pueden escribir la salida en un archivo local del nodo principal o en un blob de Azure. A continuación, los usuarios pueden utilizar otras herramientas para analizar más el resultado de las consultas de subárbol.

####Genere los resultados de consulta de subárbol en un archivo local. 

Para generar los resultados de consultas de subárbol en un directorio local del nodo principal, los usuarios tienen que enviar la consulta de subárbol de la línea de comandos de Hadoop de la siguiente manera:

	`hive -e "<hive query>" > <local path in the head node>`

En el ejemplo siguiente, el resultado de la consulta de subárbol se escribe en un archivo `hivequeryoutput.txt` del directorio `C:\apps\temp`.

![Create workspace][12]

####Generar los resultados de consulta de subárbol en un blob de Azure

Los usuarios también pueden generar resultados de consulta de subárbol en un blob de Azure, dentro del contenedor predeterminado del clúster de Hadoop. La consulta de subárbol debe ser similar a la siguiente:

	`insert overwrite directory wasb:///<directory within the default container> <select clause from ...>`

En el ejemplo siguiente, el resultado de la consulta de subárbol se escribe en un directorio de blob `queryoutputdir` dentro del contenedor predeterminado del clúster de Hadoop. En este caso, solo necesita proporcionar el nombre del directorio, sin el nombre del blob. Se producirá un error si proporciona los nombres de directorio y de blob, como `wasb:///queryoutputdir/queryoutput.txt`. 

![Create workspace][13]

Si abre el contenedor predeterminado del clúster de Hadoop mediante herramientas como el Explorador de almacenamiento de Azure, verá el resultado de la consulta de subárbol siguiente. Puede aplicar el filtro (resaltado mediante un cuadro rojo) para recuperar solo el blob con letras especificadas en los nombres.

![Create workspace][14]

###2. A través del Editor de subárboles o comandos de PowerShell de Azure

Los usuarios también pueden usar la consola de consultas (editor de subárbol) escribiendo la URL en un explorador web  `https://<Hadoop cluster name>.azurehdinsight.net/Home/HiveEditor` (se le pedirá que introduzca las credenciales de clúster de Hadoop para iniciar sesión) o puede [Enviar trabajos de subárbol mediante PowerShell](hdinsight-submit-hadoop-jobs-programmatically.md#hive-powershell). 

## <a name="explore"></a>Exploración de datos, ingeniería de funciones y adaptación de parámetros de subárbol

En esta sección se describen las siguientes tareas de conversión de datos mediante los clústeres de Hadoop de HDInsight de Azure y un tema más avanzado de adaptación de algunos parámetros de subárbol para mejorar el rendimiento de las consultas de subárbol:

1. [Exploración de datos](#hive-dataexploration)
2. [Generación de características](#hive-featureengineering)
3. [Tema avanzado: ajustar parámetros de subárbol para mejorar la velocidad de consulta](#tune-parameters)

> [AZURE.NOTE] Las consultas de subárbol de ejemplo dan por hecho que se han cargado los datos a tablas de subárbol en clústeres de Hadoop de Azure HDInsight. De no ser así, siga [Crear y cargar datos en tablas de subárbol](machine-learning-data-science-hive-tables.md)  para cargar datos en tablas de subárbol primero.

###<a name="hive-dataexploration"></a>Exploración de datos
A continuación presentamos algunos scripts de subárbol que se pueden usar para explorar los datos de las tablas de subárbol.

1. Obtener el número de observaciones por partición
	`SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. Obtener el número de observaciones por día
	`SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);` 

3. Obtenga los niveles de una columna de categorías  
	`SELECT  distinct <column_name> from <databasename>.<tablename>`

4. Obtener el número de niveles de combinación de dos columnas de categorías 
	`SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. Obtener la distribución para columnas numéricas  
	`SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. Extraer registros de la combinación de dos tablas 

	    SELECT 
			a.<common_columnname1> as <new_name1>,
			a.<common_columnname2> as <new_name2>,
    		a.<a_column_name1> as <new_name3>,
    		a.<a_column_name2> as <new_name4>,
    		b.<b_column_name1> as <new_name5>,
    		b.<b_column_name2> as <new_name6>
    	FROM
    		(
    		SELECT <common_columnname1>, 
    			<common_columnname2>,
				<a_column_name1>,
				<a_column_name2>,
			FROM <databasename>.<tablename1>
			) a
			join
			(
			SELECT <common_columnname1>, 
    			<common_columnname2>,
				<b_column_name1>,
				<b_column_name2>,
			FROM <databasename>.<tablename2>
			) b
			ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

###<a name="hive-featureengineering"></a>Generación de características

En esta sección se describen maneras de generar características mediante consultas de subárbol: 

> [AZURE.NOTE]
> Cuando genere características adicionales, puede agregarlas como columnas a la tabla existente o crear una nueva tabla con las características adicionales y la clave principal, que se pueden combinar con la tabla original.  

1. [Generación de características basada en frecuencia](#hive-frequencyfeature)
2. [Riesgos de las variables de categorías en la clasificación binaria](#hive-riskfeature)
3. [Extraer características del campo de fecha y hora](#hive-datefeatures)
4. [Extraer características del campo de texto](#hive-textfeatures)
5. [Calcular la distancia entre las coordenadas GPS](#hive-gpsdistance)

####<a name="hive-frequencyfeature"></a>Generación de características basada en frecuencia

A veces, resulta valioso calcular las frecuencias de los niveles de una variable de categoría o las frecuencias de combinaciones de niveles de varias variables de categorías. Los usuarios pueden usar el script siguiente para calcular las frecuencias:

		select 
			a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
		from
		(
			select 
				<column_name1>,<column_name2>, count(*) as sub_count 
			from <databasename>.<tablename> group by <column_name1>, <column_name2>
		)a
		order by frequency desc;
	

####<a name="hive-riskfeature"></a>Riesgos de las variables de categorías en la clasificación binaria

En la clasificación binaria, a veces necesitamos convertir variables de categorías no numéricas en funciones numéricas reemplazando los niveles no numéricos por riesgos numéricos, ya que es posible que algunos modelos únicamente admitan funciones numéricas. En esta sección mostramos algunas consultas de subárbol genéricas que calculan los valores de riesgo (probabilidades de registro) de una variable de categoría. 


	    set smooth_param1=1;
	    set smooth_param2=20;
	    select 
	    	<column_name1>,<column_name2>, 
			ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
	    from
	    	(
	    	select 
	    		<column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
	    	from
	    		(
	    		select 
	    			<column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
	    		from <databasename>.<tablename> 
	    		)a
	    	group by <column_name1>, <column_name2>
	    	)b 

En este ejemplo, las variables `smooth_param1` y `smooth_param2` se establecen para suavizar los valores de riesgo calculados a partir de los datos. Los riesgos se sitúan entre -Inf e Inf. Riesgos> 0 indica la probabilidad de que el destino equivalga a 1 es mayor que 0,5. 

Después de calcularse la tabla de riesgos, los usuarios pueden asignar valores de riesgo a una tabla uniéndola a la tabla de riesgo. La consulta de unión de subárbol se ha proporcionado en la sección anterior.

####<a name="hive-datefeature"></a>Extraer características de campos de fecha y hora

El subárbol se suministra con un conjunto de UDF para el procesamiento de los campos de fecha y hora. En el subárbol, el formato de fecha y hora predeterminado es "aaaa-MM-dd 00:00:00" (al igual que "1970-01-01 12:21:32"). En esta sección, se muestran ejemplos de extracción del día del mes y del mes de un campo de fecha y hora, y ejemplos de conversión de una cadena de fecha y hora en un formato distinto del predeterminado en una cadena de fecha y hora de formato predeterminado. 

    	select day(<datetime field>), month(<datetime field>) 
		from <databasename>.<tablename>;

Esta consulta de subárbol asume que el `<campo de fecha y hora>` está en el formato de fecha y hora predeterminado.

Si un campo de fecha y hora no se encuentra en el formato predeterminado, primero necesitamos convertir el campo datetile en la marca de tiempo de Unix y, a continuación, convertir la marca de tiempo de Unix en una cadena de fecha y hora de formato predeterminado. Cuando la fecha y hora se encuentran en el formato predeterminado, los usuarios pueden aplicar los UDF de fecha y hora incrustados para extraer funciones.

		select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
		from <databasename>.<tablename>;

En esta consulta, si `<campo de fecha y hora>` tiene un patrón como `03/26/2015 12:04:39`, el `"<patrón del campo de fecha y hora>"` debería ser `"MM/dd/aaaa HH:mm:ss"`. Para probarlo, los usuarios pueden ejecutar

		select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
		from hivesampletable limit 1;

En esta consulta, `hivesampletable` incluye todos los clústeres de Hadoop de Azure HDInsight de forma predeterminada cuando se aprovisionan los clústeres. 


####<a name="hive-textfeature"></a>Extraer características de campos de texto

Supongamos que la tabla de subárbol tiene un campo de texto, que es una cadena de palabras separadas por un espacio, la siguiente consulta extraerá la longitud de la cadena y el número de palabras de la cadena.

    	select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num 
		from <databasename>.<tablename>;

####<a name="hive-gpsdistance"></a>Calcular la distancia entre las coordenadas GPS

La consulta que se proporciona en esta sección se puede aplicar directamente en los datos de viajes en taxi de Nueva York. El propósito de esta consulta es mostrar cómo aplicar las funciones matemáticas incrustadas en el subárbol para generar funciones. 

Los campos que se utilizan en esta consulta son coordenadas GPS de ubicaciones de recogida y entrega, denominadas pickup_longitude, pickup_latitude, dropoff_longitude y dropoff_latitude. Las consultas para calcular la distancia directa entre las coordenadas de recogida y entrega son:

		set R=3959;
		set pi=radians(180);
		select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, 
			${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
			*${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
			*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
			/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
			+cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
			pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance 
		from nyctaxi.trip 
		where pickup_longitude between -90 and 0
		and pickup_latitude between 30 and 90
		and dropoff_longitude between -90 and 0
		and dropoff_latitude between 30 and 90
		limit 10; 

Las ecuaciones matemáticas de cálculo de distancia entre dos coordenadas GPS pueden encontrarse [aquí](http://www.movable-type.co.uk/scripts/latlong.html), escrito por Peter Lapisu. En su Javascript, la función toRad() es simplemente `lat_or_lon*pi/180`, which converts degrees to radians. Here, `lat_or_lon` es la latitud o longitud. Puesto que el subárbol no proporciona la función `atan2`, but provides function `atan`, la `atan2` function is implemented by `atan` función de la consulta de subárbol anterior, según su definición en [Wikipedia](http://en.wikipedia.org/wiki/Atan2). 

![Create workspace][1]

[Aquí](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions) puede encontrar una lista completa de los UDF incrustados de subárbol. 

## <a name="tuning"></a> Temas avanzados: Ajustar los parámetros de subárbol para mejorar la velocidad de consulta

La configuración de parámetros predeterminados del clúster de subárbol podría no ser adecuada para las consultas de subárbol y los datos que estas consultas procesan. En esta sección se describen algunos parámetros que los usuarios pueden ajustar para mejorar el rendimiento de las consultas de subárbol. Los usuarios necesitan agregar el parámetro que optimiza las consultas antes de las consultas de procesamiento de datos. 

1. Espacio de montón de Java: Para las consultas que implican la combinación de grandes conjuntos de datos, o el procesamiento de largos registros, un error habitual es **quedarse sin espacio en el montón**. Esto se puede ajustar estableciendo parámetros `mapreduce.map.java.opts` y `mapreduce.task.io.sort.mb` en los valores deseados. Aquí tiene un ejemplo:

		set mapreduce.map.java.opts=-Xmx4096m;
		set mapreduce.task.io.sort.mb=-Xmx1024m;
	

	Este parámetro asigna 4 GB de memoria al espacio de montón de Java y también hace que la ordenación sea más eficiente al asignar más memoria para él. Es recomendable jugar con él si hay errores relacionados con el trabajo en el espacio del montón.

2. Tamaño de bloque de DFS: Este parámetro establece la unidad más pequeña de datos que el sistema de archivos almacena. Por ejemplo, si el tamaño de bloque DFS es 128 MB, a continuación, los datos de un tamaño menor que 128 MB, que también será el tamaño máximo, se almacenará en un solo bloque, mientras que a los datos mayores de 128 MB se les asignará bloques adicionales. Al elegir un tamaño de bloque muy pequeño se producirán grandes sobrecargas en Hadoop puesto que el nodo de nombre tiene que procesar muchas más solicitudes para buscar el bloque relevante relacionado con el archivo. Una configuración recomendada al tratar con datos de gigabytes (o mayores) es:

		set dfs.block.size=128m;

3. Optimización de la operación de combinación en el subárbol: Aunque las operaciones de combinación en el marco de asignación/reducción normalmente tienen lugar en la fase de reducción, en ocasiones, se pueden lograr ganancias enormes mediante la programación de combinaciones en la fase de asignación (también denominada "combinaciones de asignaciones"). Para indicar al subárbol que haga esto siempre que sea posible, podemos establecer:

		set hive.auto.convert.join=true;

4. Especificación del número de asignadores al subárbol: Aunque Hadoop permite al usuario establecer el número de reductores, no suele establecer el número de asignadores. Un truco que permite cierto grado de control sobre este número es elegir las variables de hadoop, mapred.min.split.size y mapred.max.split.size. El tamaño de cada tarea de asignación está determinado por:

		num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

	Normalmente, el valor predeterminado de mapred.min.split.size es 0, el de mapred.max.split.size es Long.MAX y de dfs.block.size es 64 MB. Como podemos ver, dado el tamaño de los datos, el ajuste de estos parámetros mediante su "configuración" nos permite optimizar el número de asignadores que se usan. 

5. A continuación se mencionan algunas otras opciones avanzadas más para optimizar el rendimiento del subárbol. Estas permiten la configuración de la memoria asignada para asignar y reducir las tareas, y pueden ser útiles para aumentar el rendimiento. Tenga en cuenta que el  `mapreduce.reduce.memory.mb` no puede ser mayor que el tamaño de la memoria física de cada nodo de trabajo del clúster de Hadoop.

		set mapreduce.map.memory.mb = 2048;
		set mapreduce.reduce.memory.mb=6144;
		set mapreduce.reduce.java.opts=-Xmx8192m;
		set mapred.reduce.tasks=128;
		set mapred.tasktracker.reduce.tasks.maximum=128;

[1]: ./media/machine-learning-data-science-hive-queries/atan2new.png
[10]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-1.png
[11]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-2.png
[12]: ./media/machine-learning-data-science-hive-queries/output-hive-results-1.png
[13]: ./media/machine-learning-data-science-hive-queries/output-hive-results-2.png
[14]: ./media/machine-learning-data-science-hive-queries/output-hive-results-3.png
[15]: ./media/machine-learning-data-science-hive-queries/run-hive-queries-3.png



<!--HONumber=49-->