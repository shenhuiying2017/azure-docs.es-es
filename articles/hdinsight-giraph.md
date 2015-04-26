<properties 
	pageTitle="Uso de Apache Giraph con HDInsight de Azure" 
	description="Aprendizaje del uso de Apache Giraph para realizar procesamiento gráfico con HDInsight de Azure" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/14/2014" 
	ms.author="larryfr"/>

#Aprendizaje del uso de Apache Giraph con Azure HDInsight (Hadoop)

[Apache Giraph][giraph] permite realizar procesamiento gráfico mediante Hadoop, y se puede usar con Azure HDInsight. 

Los gráficos modelan las relaciones entre los objetos, como las conexiones entre los enrutadores en una red de gran tamaño como Internet, o las relaciones entre personas de las redes sociales (conocidas en ocasiones como gráficos sociales). El procesamiento gráfico le permite razonar sobre las relaciones entre los objetos de un gráfico. En este sentido, le ayuda por ejemplo a:

* Identificar los posibles amigos según las relaciones actuales

* Identificar la ruta más corta entre dos equipos de una red

* Calcular la posición de las páginas web.

##Aprenderá a:

* [Compilar e implementar Apache Giraph en un clúster de HDInsight](#build)

* [Ejecutar el ejemplo SimpleShortestPathsComputation](#run)

	Para obtener una lista de otros ejemplos que se proporcionan con Giraph, consulte [Paquete org.apache.giraph.examples](https://giraph.apache.org/apidocs/org/apache/giraph/examples/package-summary.html).

* [Solucionar los problemas con los que pueda encontrarse](#tshoot)

##Requisitos

* Un clúster de Azure HDInsight, versión 3.0 o 3.1

* [Git](http://git-scm.com/)

* Java 1.6

* [Maven](http://maven.apache.org/) 3 o superior

##<a id="build"></a>Compilación e implementación de Giraph

Giraph no se proporciona como parte del clúster de HDInsight, así que se debe compilar desde el origen.  Puede encontrar más información sobre la compilación de Giraph en el [repositorio de Giraph.](https://github.com/apache/giraph)

1. Actualmente (14-07-2014,) Giraph requiere una revisión para funcionar con el almacenamiento de archivos WASB que usa HDInsight. La revisión se ha enviado al proyecto Apache Giraph, pero aún no se ha aceptado. Descargue la revisión desde la sección de __datos adjuntos__ de [GIRAPH-930](https://issues.apache.org/jira/browse/GIRAPH-930) y guárdela en la unidad local como __giraph-930.diff__.

1. Desde una línea de comandos, use el siguiente comando Git para crear un clon del repositorio de Giraph.

		git clone https://github.com/apache/giraph.git

2. Cambie los directorios por el directorio __giraph__ creado en la operación de clonación del paso 2.

		cd giraph

3. Incorpore la revisión al repositorio local mediante el siguiente comando.

		git apply giraph-930.diff

	Sustituya __giraph-930.diff__ por la ruta de acceso al archivo que ha creado en el paso 1.

3. Compile Giraph para su versión de clúster de HDInsight mediante uno de los siguientes comandos.

	* Para __HDInsight 3.0__ (Hadoop 2.2)

			mvn package -Phadoop_0.20.203 - DskipTests

	* Para __HDInsight 3.1__ (Hadoop 2.4)

			mvn package -Phadoop_0.23 -DskipTests

	Una vez finalizada la compilación, encontrará el archivo JAR de ejemplos en __\giraph\giraph-examples\target__.

4. Cargue dicho archivo en el almacenamiento principal del clúster de HDInsight mediante [Azure PowerShell][aps] y las [Herramientas de HDInsight][tools].

		Add-HDInsightFile giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar example/jars/giraph.jar clustername

	Sustituya __giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar__ por la ruta de acceso y el nombre del archivo JAR producido en el paso anterior, y __clustername__ por el nombre del clúster de HDInsight. Por ejemplo, si compila el paquete con el parámetro `-Phadoop_0.20.203`, el nombre del archivo JAR incluirá __hadoop-0.20.203__.

	Cuando finalice el comando, el archivo JAR se habrá cargado en wasb:///example/jars/giraph.jar.

	> [AZURE.NOTE] Para ver una lista de utilidades que se pueden usar para cargar archivos en HDInsight, consulte [Carga de datos para trabajos de Hadoop en HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-upload-data/).

##<a id="run"></a>Ejecución del ejemplo

El ejemplo SimpleShortestPathsComputation demuestra la implementación básica de [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf) para hallar la ruta más corta posible entre los objetos de un gráfico. Use los siguientes pasos para cargar los datos de ejemplo, ejecutar un trabajo mediante el ejemplo SimpleShortestPathsComputation y luego ver los resultados.

> [AZURE.NOTE] El origen de este y otros ejemplos está disponible en [release-1.1 branch](https://github.com/apache/giraph/tree/release-1.1) del [repositorio de GitHub](https://github.com/apache/giraph).

1. Cree un nuevo archivo llamado __tiny\_graph.txt__. Debe contener las siguientes líneas.

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Estos datos describen una relación entre los objetos de un [gráfico dirigido](http://en.wikipedia.org/wiki/Directed_graph) mediante el formato `[source_id,source_value,[[dest_id], [edge_value],...]]`. Cada línea representa una relación entre un __source\_id__ y uno o varios objetos __dest\_id__. El __edge\_value__ (o peso) se puede considerar como la fuerza o la distancia de la conexión entre __source\_id__ y __dest\_id__. 

	Extrapolados, y usando el valor (o peso) como la distancia entre los objetos, los datos anteriores podrían parecerse a estos.

	![tiny_graph.txt drawn as circles with lines of varying distance between](.\media\hdinsight-giraph\giraph-graph.png)


2. Cargue el archivo __tiny\_graph.txt__ en el almacenamiento principal del clúster de HDInsight mediante [Azure PowerShell][aps] y las [herramientas de HDInsight][tools].

		Add-HDInsightFile tiny_graph.txt example/data/tiny_graph.txt clustername

	Sustituya el nombre del clúster por el nombre del clúster de HDInsight.

3. Utilice el siguiente PowerShell para ejecutar el ejemplo __SimpleShortstPathsComputation__, mediante el uso del archivo __tiny\_graph.txt__ como entrada. Para ello, será necesario que haya instalado y configurado [Azure PowerShell][aps].

		$clusterName = "clustername"
		# Giraph examples JAR
		$jarFile = "wasb:///example/jars/giraph.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation", `
		                "-ca", "mapred.job.tracker=headnodehost:9010", `
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat", `
		                "-vip", "wasb:///example/data/tinygraph.txt", `
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat", `
		                "-op",  "wasb:///example/output/shortestpaths", `
		                "-w", "2"
		# Create the definition
		$jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
		  -JarFile $jarFile `
		  -ClassName "org.apache.giraph.GiraphRunner" `
		  -Arguments $jobArguments
		
		# Run the job, write output to the PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	En el ejemplo anterior, sustituya __clustername__ por el nombre del clúster de HDInsight.

###View the results

Cuando el trabajo se haya completado, los resultados se almacenarán en la carpeta __wasb:///example/out/shotestpaths__ como archivos __part-m-#####__. Use [Azure PowerShell][aps] y las [Herramientas de HDInsight][tools] para descargar los archivos de salida.

	Find-HDInsightFile example/output/shortestpaths/part* clustername | foreach-object { Get-HDInsightFile $_.name .  itsfullofstorage }
	Cat example/output/shortestpaths/part*

De esta forma se creará la estructura de directorios __example/output/shortestpaths__ en el directorio actual y se descargarán los archivos que comienzan por __part__. El cmdlet __Cat__ mostrará entonces el contenido de los archivos, que debe ser parecido al siguiente.

	0	1.0
	4	5.0
	2	2.0
	1	0.0
	3	1.0

El ejemplo SimpleShortestPathComputation se ha codificado de forma rígida para comenzar por el Id. de objeto 1 y encontrar la ruta más corta a los demás objetos. Así que la salida se debe leer como  `destination_id distance`, donde la distancia es el valor (o peso) de los bordes recorridos entre el identificador de objeto 1 y el identificador de destino.

Visualizando esto, puede verificar los resultados recorriendo las rutas más cortas entre el Id. 1 y todos los demás objetos. Tenga en cuenta que la ruta más corta entre el Id. 1 y el Id. 4 es 5. Esta es la distancia total entre <span style="color:orange">Id. 1 y 3</span>y después entre <span style="color:red">Id. 3 y 4</span>.

![Drawing of objects as circles with shortest paths drawn between](.\media\hdinsight-giraph\giraph-graph-out.png)

##<a id="tshoot"></a>Solución de problemas

###El directorio de salida ya existe

Los trabajos de Giraph crean el directorio de salida especificado en tiempo de ejecución. Si el directorio ya existe, se mostrará un error al respecto.

Si desea ejecutar un trabajo varias veces, debe quitar el directorio de salida entre un trabajo y otro o especificar uno diferente para cada trabajo.

###<a id="cmd"></a>Uso de la línea de comandos de Hadoop

Aunque en este artículo se demuestra cómo ejecutar un trabajo de Giraph a través de PowerShell, también puede ejecutar el trabajo mediante la línea de comandos de Hadoop.

> [AZURE.NOTE] La línea de comandos de Hadoop solo está disponible al conectarse al clúster de HDInsight mediante Escritorio remoto.
> 
> Las sesiones de Escritorio remoto a recursos de proceso de Azure como el clúster de HDInsight puede que solo funcionen desde clientes de Escritorio remoto de Windows.

Para conectarse al clúster de HDInsight, lleve a cabo los siguientes pasos:

1. Mediante el [Portal de administración de Azure](https://manage.windowsazure.com), seleccione el clúster de HDInsight y luego seleccione __Configuración__.

2. En la parte inferior de la página, seleccione __Habilitar de forma remota__ y proporcione el nombre de usuario, la contraseña y la fecha de caducidad de la conexión a Escritorio remoto.

3. Una vez que la solicitud para habilitar Escritorio remoto se ha procesado, una nueva entrada a __Conectar__ aparecerá en la parte inferior de la página. Selecciónela para descargar el archivo .RDP para la sesión de Escritorio remoto.

4. El archivo .RDP se puede guardar o abrir inmediatamente para iniciar el cliente de Escritorio remoto. Durante el proceso de conexión, se le pedirá que proporcione el nombre de usuario y la contraseña que usa para habilitar la conexión a Escritorio remoto.

5. Una vez conectado, use el icono de __línea de comandos de Hadoop__ en el escritorio para iniciar la línea de comandos de Hadoop.

6. En el siguiente ejemplo se demuestra cómo copiar el archivo __giraph.jar__ en el nodo principal de clúster, y luego ejecutar el trabajo mediante la línea de comandos de Hadoop.

		hadoop fs -copyToLocal wasb:///example/jar/giraph.jar
		hadoop jar giraph.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca "mapred.job.tracker=headnodehost:9010" -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip wasb:///example/data/tinygraph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op wasb:///example/output/shortestpaths -w 2


###Versiones antiguas de HDInsight

Si desea usar Giraph con versiones antiguas de HDInsight, debe compilarlo en la versión específica de Hadoop que admita esa versión. Consulte [Novedades en las versiones de clúster proporcionadas por HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/) para determinar la versión de Hadoop que corresponde a su versión de HDInsight.

Además, las versiones más antiguas de HDInsight pueden necesitar que ejecute el trabajo de Giraph desde la línea de comandos de Hadoop. Si recibe errores al ejecutar el trabajo desde PowerShell, intente ejecutar el trabajo desde la [línea de comandos de Hadoop](#cmd).

##Pasos siguientes

Ahora que ha aprendido a usar Giraph con HDInsight, pruebe [Pig][] y [Hive][] con HDInsight.

[giraph]: http://giraph.apache.org
[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
[pig]: http://azure.microsoft.com/documentation/articles/hdinsight-use-pig/
[hive]: http://azure.microsoft.com/documentation/articles/hdinsight-use-hive/

<!--HONumber=42-->
