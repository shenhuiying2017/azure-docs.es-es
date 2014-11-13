<properties title="Uso de Giraph con HDInsight" pageTitle="Uso de Apache Giraph con HDInsight de Azure" description="Aprendizaje del uso de Apache Giraph para realizar procesamiento gr&aacute;fico con HDInsight de Azure" metaKeywords="Azure HDInsight Apache Giraph, hdinsight giraph, hdinsight graph, hadoop giraph, azure hadoop, hadoop graph" services="hdinsight" solutions="big-data" documentationCenter="" authors="larryfr" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/14/2014" ms.author="larryfr" />

## Aprendizaje del uso de Apache Giraph con Azure HDInsight (Hadoop)

[Apache Giraph][Apache Giraph] permite realizar procesamiento gráfico mediante Hadoop, y se puede usar con Azure HDInsight.

Los gráficos modelan las relaciones entre los objetos, como las conexiones entre los enrutadores en una red de gran tamaño como Internet, o las relaciones entre personas de las redes sociales (conocidas en ocasiones como gráficos sociales). El procesamiento gráfico le permite razonar sobre las relaciones entre los objetos de un gráfico. En este sentido, le ayuda por ejemplo a:

-   Identificar los posibles amigos según las relaciones actuales

-   Identificar la ruta más corta entre dos equipos de una red

-   Calcular la posición de las páginas web.

## Aprenderá a:

-   [Compilar e implementar Apache Giraph en un clúster de HDInsight][Compilar e implementar Apache Giraph en un clúster de HDInsight]

-   [Ejecutar el ejemplo SimpleShortestPathsComputation][Ejecutar el ejemplo SimpleShortestPathsComputation]

    Para obtener una lista de otros ejemplos que se proporcionan con Giraph, consulte [Paquete org.apache.giraph.examples][Paquete org.apache.giraph.examples].

-   [Solucionar los problemas con los que pueda encontrarse][Solucionar los problemas con los que pueda encontrarse]

## Requisitos

-   Un clúster de Azure HDInsight, versión 3.0 o 3.1

-   [Git][Git]

-   Java 1.6

-   [Maven][Maven] 3 o superior

## <span id="build"></span></a>Compilación e implementación de Giraph

Giraph no se proporciona como parte del clúster de HDInsight, así que se debe compilar desde el origen. Puede encontrar más información sobre la compilación de Giraph en el [repositorio de Giraph][repositorio de Giraph].

1.  Actualmente (14-07-2014,) Giraph requiere una revisión para funcionar con el almacenamiento de archivos WASB que usa HDInsight. La revisión se ha enviado al proyecto Apache Giraph, pero aún no se ha aceptado. Descargue la revisión desde la sección de **datos adjuntos** de [GIRAPH-930][GIRAPH-930] y guárdela en la unidad local como **giraph-930.diff**.

2.  Desde una línea de comandos, use el siguiente comando Git para crear un clon del repositorio de Giraph.

        git clone https://github.com/apache/giraph.git

3.  Cambie los directorios por el directorio **giraph** creado en la operación de clonación del paso 2.

        cd giraph

4.  Incorpore la revisión al repositorio local mediante el siguiente comando.

        git apply giraph-930.diff

    Sustituya **giraph-930.diff** por la ruta de acceso al archivo que ha creado en el paso 1.

5.  Compile Giraph para su versión de clúster de HDInsight mediante uno de los siguientes comandos.

    -   Para **HDInsight 3,0** (Hadoop 2,2)

            mvn package -Phadoop_0.20.203 - DskipTests

    -   Para **HDInsight 3.1** (Hadoop 2.4)

            mvn package -Phadoop_0.23 -DskipTests

    Una vez finalizada la compilación, encontrará el archivo JAR de ejemplos en **\\giraph\\giraph-examples\\target**.

6.  Cargue dicho archivo en el almacenamiento principal del clúster de HDInsight mediante [Azure PowerShell][Azure PowerShell] y las [Herramientas de HDInsight][Herramientas de HDInsight].

        Add-HDInsightFile giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar example/jars/giraph.jar clustername

    Sustituya **giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar** por la ruta de acceso y el nombre del archivo JAR producido en el paso anterior, y **clustername** por el nombre del clúster de HDInsight. Por ejemplo, si compila el paquete con el parámetro `-Phadoop_0.20.203`, el nombre del archivo JAR incluirá **hadoop-0.20.203**.

    Cuando finalice el comando, el archivo JAR se habrá cargado en wasb:///example/jars/giraph.jar.

    > [WACOM.NOTE] Para ver una lista de utilidades que se pueden usar para cargar archivos en HDInsight, consulte [Carga de datos en HDInsight][Carga de datos en HDInsight].

## <span id="run"></span></a>Ejecución del ejemplo

El ejemplo SimpleShortestPathsComputation demuestra la implementación básica de [Pregel][Pregel] para hallar la ruta más corta posible entre los objetos de un gráfico. Use los siguientes pasos para cargar los datos de ejemplo, ejecutar un trabajo mediante el ejemplo SimpleShortestPathsComputation y luego ver los resultados.

> [WACOM.NOTE] El origen de este y otros ejemplos está disponible en [release-1.1 branch][release-1.1 branch] del [repositorio de GitHub][repositorio de Giraph].

1.  Cree un nuevo archivo llamado **tiny\_graph.txt**. Debe contener las siguientes líneas.

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Estos datos describen una relación entre los objetos de un [gráfico dirigido][gráfico dirigido], mediante el formato `[source_id,source_value,[[dest_id], [edge_value],...]]`. Cada línea representa una relación entre un **source\_id** y uno o varios objetos **dest\_id**. El **edge\_value** (o peso) se puede considerar como la fuerza o la distancia de la conexión entre el **source\_id** y el **dest\_id**.

    Extrapolados, y usando el valor (o peso) como la distancia entre los objetos, los datos anteriores podrían parecerse a estos.

    ![tiny\_graph.txt drawn as circles with lines of varying distance between][tiny\_graph.txt drawn as circles with lines of varying distance between]

2.  Cargue el archivo **tiny\_graph.txt** en el almacenamiento principal del clúster de HDInsight mediante[Azure PowerShell][Azure PowerShell] y las [Herramientas de HDInsight][Herramientas de HDInsight].

        Add-HDInsightFile tiny_graph.txt example/data/tiny_graph.txt clustername

    Sustituya el nombre del clúster por el nombre del clúster de HDInsight.

3.  Use el siguiente PowerShell para ejecutar el ejemplo **SimpleShortstPathsComputation**, mediante el uso del archivo **tiny\_graph.txt** como entrada. Para ello, será necesario que haya instalado y configurado [Azure PowerShell][Azure PowerShell].

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

    En el ejemplo anterior, sustituya **clustername** por el nombre del clúster de HDInsight.

### View the results

Cuando el trabajo se haya completado, los resultados se almacenarán en la carpeta **wasb:///example/out/shotestpaths** como archivos **part-m-\#\#\#\#\#**. Use [Azure PowerShell][Azure PowerShell] y las [Herramientas de HDInsight][Herramientas de HDInsight] para descargar los archivos de salida.

    Find-HDInsightFile example/output/shortestpaths/part* clustername | foreach-object { Get-HDInsightFile $_.name .  itsfullofstorage }
    Cat example/output/shortestpaths/part*

De esta forma se creará la estructura de directorios **example/output/shortestpaths** en el directorio actual y se descargarán los archivos que comienzan por **part**. El cmdlet **Cat** mostrará entonces el contenido de los archivos, que debe ser parecido al siguiente.

    0   1.0
    4   5.0
    2   2.0
    1   0.0
    3   1.0

El ejemplo SimpleShortestPathComputation se ha codificado de forma rígida para comenzar por el Id. de objeto 1 y encontrar la ruta más corta a los demás objetos. Así que la salida se debe leer como `destination_id distance`, donde la distancia es el valor (o peso) de los bordes recorridos entre el Id. de objeto 1 y el Id. de destino.

Visualizando esto, puede verificar los resultados recorriendo las rutas más cortas entre el Id. 1 y todos los demás objetos. Observe que la ruta más corta entre el Id. 1 y el Id. 4 es 5. Esta es la distancia total entre <span style="color:orange">Id. 1 y 3</span>, y luego <span style="color:red">Id. 3 y 4</span>.

![Drawing of objects as circles with shortest paths drawn between][Drawing of objects as circles with shortest paths drawn between]

## <span id="tshoot"></span></a> Solución de problemas

### El directorio de salida ya existe

Los trabajos de Giraph crean el directorio de salida especificado en tiempo de ejecución. Si el directorio ya existe, se mostrará un error al respecto.

Si desea ejecutar un trabajo varias veces, debe quitar el directorio de salida entre un trabajo y otro o especificar uno diferente para cada trabajo.

### <span id="cmd"></span></a>Uso de la línea de comandos de Hadoop

Aunque en este artículo se demuestra cómo ejecutar un trabajo de Giraph a través de PowerShell, también puede ejecutar el trabajo mediante la línea de comandos de Hadoop.

> [WACOM.NOTE] La línea de comandos de Hadoop solo está disponible al conectarse al clúster de HDInsight mediante Escritorio remoto.
>
> Las sesiones de Escritorio remoto a recursos de proceso de Azure como el clúster de HDInsight puede que solo funcionen desde clientes de Escritorio remoto de Windows.

Para conectarse al clúster de HDInsight, lleve a cabo los siguientes pasos:

1.  Mediante el [Portal de administración de Azure][Portal de administración de Azure], seleccione el clúster de HDInsight y luego seleccione **Configuración**.

2.  En la parte inferior de la página, seleccione **Habilitar de forma remota** y proporcione el nombre de usuario, la contraseña y la fecha de caducidad de la conexión a Escritorio remoto.

3.  Una vez que la solicitud para habilitar Escritorio remoto se ha procesado, una nueva entrada a **Conectar** aparecerá en la parte inferior de la página. Selecciónela para descargar el archivo .RDP para la sesión de Escritorio remoto.

4.  El archivo .RDP se puede guardar o abrir inmediatamente para iniciar el cliente de Escritorio remoto. Durante el proceso de conexión, se le pedirá que proporcione el nombre de usuario y la contraseña que usa para habilitar la conexión a Escritorio remoto.

5.  Una vez conectado, use el icono de **línea de comandos de Hadoop** en el escritorio para iniciar la línea de comandos de Hadoop.

6.  En el siguiente ejemplo se demuestra cómo copiar el archivo **giraph.jar** en el nodo cabeza de clúster, y luego ejecutar el trabajo mediante la línea de comandos de Hadoop.

        hadoop fs -copyToLocal wasb:///example/jar/giraph.jar
        hadoop jar giraph.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca "mapred.job.tracker=headnodehost:9010" -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip wasb:///example/data/tinygraph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op wasb:///example/output/shortestpaths -w 2

### Versiones antiguas de HDInsight

Si desea usar Giraph con versiones antiguas de HDInsight, debe compilarlo en la versión específica de Hadoop que admita esa versión. Consulte [¿Cuáles son las novedades en las versiones de clústeres proporcionadas por HDInsight?][¿Cuáles son las novedades en las versiones de clústeres proporcionadas por HDInsight?] para determinar la versión de Hadoop que corresponde a su versión de HDInsight.

Además, las versiones más antiguas de HDInsight pueden necesitar que ejecute el trabajo de Giraph desde la línea de comandos de Hadoop. Si recibe errores al ejecutar el trabajo desde PowerShell, intente ejecutar el trabajo desde la [línea de comandos de Hadoop][línea de comandos de Hadoop].

## Pasos siguientes

Ahora que ha aprendido a usar Giraph con HDInsight, pruebe [Pig][Pig] y [Hive][Hive] con HDInsight.

  [Apache Giraph]: http://giraph.apache.org
  [Compilar e implementar Apache Giraph en un clúster de HDInsight]: #build
  [Ejecutar el ejemplo SimpleShortestPathsComputation]: #run
  [Paquete org.apache.giraph.examples]: https://giraph.apache.org/apidocs/org/apache/giraph/examples/package-summary.html
  [Solucionar los problemas con los que pueda encontrarse]: #tshoot
  [Git]: http://git-scm.com/
  [Maven]: http://maven.apache.org/
  [repositorio de Giraph]: https://github.com/apache/giraph
  [GIRAPH-930]: https://issues.apache.org/jira/browse/GIRAPH-930
  [Azure PowerShell]: http://azure.microsoft.com/es-es/documentation/articles/install-configure-powershell/
  [Herramientas de HDInsight]: https://github.com/Blackmist/hdinsight-tools
  [Carga de datos en HDInsight]: http://azure.microsoft.com/es-es/documentation/articles/hdinsight-upload-data/
  [Pregel]: http://people.apache.org/~edwardyoon/documents/pregel.pdf
  [release-1.1 branch]: https://github.com/apache/giraph/tree/release-1.1
  [gráfico dirigido]: http://en.wikipedia.org/wiki/Directed_graph
  [tiny\_graph.txt drawn as circles with lines of varying distance between]: .\media\hdinsight-giraph\giraph-graph.png
  [Drawing of objects as circles with shortest paths drawn between]: .\media\hdinsight-giraph\giraph-graph-out.png
  [Portal de administración de Azure]: https://manage.windowsazure.com
  [¿Cuáles son las novedades en las versiones de clústeres proporcionadas por HDInsight?]: http://azure.microsoft.com/es-es/documentation/articles/hdinsight-component-versioning/
  [línea de comandos de Hadoop]: #cmd
  [Pig]: http://azure.microsoft.com/es-es/documentation/articles/hdinsight-use-pig/
  [Hive]: http://azure.microsoft.com/es-es/documentation/articles/hdinsight-use-hive/
