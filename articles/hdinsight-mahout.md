<properties title="Generaci&oacute;n de recomendaciones con Mahout" pageTitle="Generaci&oacute;n de recomendaciones de pel&iacute;culas usando Mahout con HDInsight (Hadoop) de Microsoft Azure" description="Aprenda a usar la biblioteca de aprendizaje autom&aacute;tico de Apache Mahout para generar recomendaciones de pel&iacute;culas con HDInsight (Hadoop)." metaKeywords="Azure hdinsight mahout, Azure hdinsight machine learning, azure hadoop mahout, azure hadoop machine learning" services="hdinsight" solutions="" documentationCenter="big-data" authors="larryfr" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Generación de recomendaciones de películas mediante Apache Mahout con HDInsight (Hadoop)

Aprenda a usar la biblioteca de aprendizaje automático de [Apache Mahout][Apache Mahout] para generar recomendaciones de películas con HDInsight de Microsoft Azure (Hadoop).

> [WACOM.NOTE] Para usar la información de este artículo, debe tener un clúster de HDInsight. Para obtener información sobre cómo crear uno, consulte [Introducción al uso de HDInsight de Azure][Introducción al uso de HDInsight de Azure].
>
> Mahout se proporciona con los clústeres de HDInsight 3.1. Si va a usar una versión anterior de HDInsight, consulte [Instalación de Mahout][Instalación de Mahout] antes de continuar.

## <a name="learn"></a>Lo que va a aprender

Mahout es una biblioteca de [aprendizaje automático][aprendizaje automático] para Apache Hadoop. Mahout contiene algoritmos para el procesamiento de datos, como filtrado, clasificación y agrupación en clústeres. En este artículo usará un motor de recomendaciones para generar recomendaciones de películas basadas en películas que sus amigos han visto. También aprenderá a realizar clasificaciones con un bosque de decisiones. Esto le enseñará a:

-   Ejecutar trabajos de Mahout desde PowerShell

-   Ejecutar trabajos de Mahout desde la línea de comandos de Hadoop

-   Instalar Mahout en clústeres de HDInsight 2.0 y 3.0

## En este artículo

-   [Generación de recomendaciones mediante PowerShell][Generación de recomendaciones mediante PowerShell]
-   [Clasificación de datos mediante la línea de comandos de Hadoop][Clasificación de datos mediante la línea de comandos de Hadoop]
-   [Solución de problemas][Solución de problemas]

## <a name="recommendations"></a>Generación de recomendaciones mediante PowerShell

> [WACOM.NOTE] Aunque el trabajo usado en esta sección funciona con PowerShell, muchas de las clases proporcionadas con Mahout no funcionan actualmente con PowerShell y se deben ejecutar mediante la línea de comandos de Hadoop. Para obtener un listado de las clases que no funcionan con PowerShell, consulte la sección [Solución de problemas][Solución de problemas].
>
> Para ver un ejemplo del uso de la línea de comandos de Hadoop para ejecutar trabajos de Mahout, consulte [Clasificación de datos mediante la línea de comandos de Hadoop][Clasificación de datos mediante la línea de comandos de Hadoop].

Una de las funciones que proporciona Mahout es un motor de recomendaciones. Este motor acepta datos en formato `userID`, `itemId`, `prefValue` (la preferencia de los usuarios por el elemento). Mahout puede realizar entonces análisis de ocurrencias conjuntas para determinar que *los usuarios que tienen predilección por un elemento también la tienen por estos otros elementos*. A continuación, Mahout determinará los usuarios con preferencias de elementos similares, lo que se puede usar para realizar recomendaciones.

A continuación se muestra un ejemplo muy sencillo usando películas:

-   **Ocurrencia conjunta**: A José, Alicia y Roberto les gusta *La Guerra de las galaxias*, *El imperio contraataca* y *El retorno del Jedi*. Mahout determinaría que a los usuarios que les gusta alguna de estas películas también les gustan las otras dos.

-   **Ocurrencia conjunta**: A Roberto y Alicia también les gusta *La amenaza fantasma*, *El ataque de los clones* y *La venganza de los Sith*. Mahout determinaría que a los usuarios que les gustan las tres películas anteriores también les gustan estas tres.

-   **Recomendación basada en similitud**: Como a José le gustan las tres primeras, Mahout examinaría películas que a otros con preferencias similares les han gustado, pero que José no ha visto (gustado/valorado). En este caso, Mahout recomendaría *La amenaza fantasma*, *El ataque de los clones* y *La venganza de los Sith*.

### Carga de los datos

Para su comodidad, GroupLens Research proporciona [calificaciones de películas][calificaciones de películas] en un formato compatible con Mahout.

1.  Descargue el archivo [MovieLens 100k][MovieLens 100k], que contiene 100.000 calificaciones de 1000 usuarios de 1700 películas.

2.  Extraiga el archivo. Debe contener un directorio **ml-100k** con muchos archivos de datos que van precedidos por **u.**. El archivo que se analizará mediante Mahout es **u.data**. La estructura de datos de este archivo es `userID`, `movieID`, `userRating` y `timestamp`. A continuación se muestra un ejemplo de los datos.

        196 242 3   881250949
        186 302 3   891717742
        22  377 1   878887116
        244 51  2   880606923
        166 346 1   886397596

3.  Cargue el archivo **u.data** en **example/data/u.data** en el clúster de HDInsight. Si tiene [Azure PowerShell][Azure PowerShell], puede usar el módulo de PowerShell [HDInsight-Tools][HDInsight-Tools] para cargar el archivo. Para ver otras maneras de cargar los archivos, consulte [Carga de datos para trabajos de Hadoop en HDInsight][Carga de datos para trabajos de Hadoop en HDInsight]. A continuación se demuestra el uso de `Add-HDInsightFile` para cargar el archivo.

        PS C:PS C:\> Add-HDInsightFile -LocalPath "path\to\u.data" -DestinationPath "example/data/u.data" -ClusterName "your cluster name"gt; Add-HDInsightFile -LocalPath "path\to\u.data" -DestinationPath "example/data/u.data" -ClusterName "your cluster name"

    Esta acción cargará el archivo **u.data** en **example/data/u.data** en el almacenamiento predeterminado del clúster. Luego podemos acceder a estos datos mediante el URI **wasb:///example/data/u.data** de los trabajos de HDInsight.

### Ejecución del trabajo

Use el siguiente script de PowerShell para ejecutar un trabajo mediante el motor de recomendaciones de Mahout con el archivo **u.data** cargado anteriormente.

    # The HDInsight cluster name.
    $clusterName = "the cluster name"

    # The location of the Mahout jar file.
    $jarFile = "file:///c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar"
    # NOTE: The version number portion of the file path
    # may change in future versions of HDInsight.
    # Use the following to find the location and name
    # of the Mahout jar on HDInsight 3.1, and modify
    # the $jarFile= line to point to it.
    #
    # Use-AzureHDInsightCluster -Name $clusterName
    # $jarFile = Invoke-Hive -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'
    #
    # If you are using an earlier version of HDInsight,
    # set $jarFile to the jar file you
    # uploaded.
    # For example,
    # $jarFile = "wasb:///example/jars/mahout-core-0.9-job.jar"

    # The arguments for this job
    # * input - the path to the data uploaded to HDInsight
    # * output - the path to store output data
    # * tempDir - the directory for temp files
    $jobArguments = "-s", "SIMILARITY_COOCCURRENCE",
                    "--input", "wasb:///example/data/u.data",
                    "--output", "wasb:///example/out",
                    "--tempDir", "wasb:///temp/mahout"

    # Create the job definition
    $jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
      -JarFile $jarFile `
      -ClassName "org.apache.mahout.cf.taste.hadoop.item.RecommenderJob" `
      -Arguments $jobArguments

    # Start the job
    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition

    # Wait on the job to complete
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureHDInsightJob -Job $job

    # Write out any error information
    Write-Host "STDERR"
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError

> [WACOM.NOTE] Los trabajos de Mahout no eliminan los datos temporales creados durante el procesamiento del trabajo. Este es el motivo de que se especifique el parámetro `--tempDir` en el trabajo de ejemplo, para aislar los archivos temporales en una ruta específica de forma que sea fácil eliminarlos.
>
> Para eliminar estos archivos, puede usar una de las utilidades mencionadas en [Carga de datos para trabajos de Hadoop en HDInsight][Carga de datos para trabajos de Hadoop en HDInsight]. O bien, utilice la función `Remove-HDInsightFile` del script de PowerShell [HDInsight-Tools][HDInsight-Tools].
>
> Si no elimina los archivos temporales, o el archivo de salida, recibirá un error al ejecutar de nuevo el trabajo.

El trabajo de Mahout no devuelve la salida a STDOUT, sino que la almacena en el directorio de salida especificado como **part-r-00000**. Para descargar y ver el archivo, use la función `Get-HDInsightFile` del módulo de PowerShell [HDInsight-Tools][HDInsight-Tools].

A continuación se muestra un ejemplo del contenido del archivo:

    1   [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2   [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3   [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4   [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

La primera columna es `userID`. Los valores contenidos en '[' y ']' son `movieId`:`recommendationScore`.

### Visualización de la salida

Aunque puede que la salida generada esté bien para usarse en una aplicación, es ilegible para el ojo humano. Algunos de los otros archivos extraídos anteriormente en la carpeta **ml-100k** se pueden usar para resolver el `movieId` en el nombre de una película. Aunque hay un script de Python que realiza está función que está incluido en la carpeta **ml-100k** (**show\_recommendations.py**), puede usar también el siguiente script de PowerShell.

    <#
    .SYNOPSIS
        Displays recommendations for movies.
    .DESCRIPTION
        Displays recommendations generated by Mahout
        with HDInsight example in a human readable format.
    .EXAMPLE
        .\Show-Recommendation -userId 4
            -userDataFile "u.data"
            -movieFile "u.item"
            -recommendationFile "output.txt"
    #>

    [CmdletBinding(SupportsShouldProcess = $true)]
    param(
        #The user ID
        [Parameter(Mandatory = $true)]
        [String]$userId,

        [Parameter(Mandatory = $true)]
        [String]$userDataFile,

        [Parameter(Mandatory = $true)]
        [String]$movieFile,

        [Parameter(Mandatory = $true)]
        [String]$recommendationFile
    )
    # Read movie ID & description into hash table
    Write-Host "Reading movies descriptions" -ForegroundColor Green
    $movieById = @{}
    foreach($line in Get-Content $movieFile)
    {
        $tokens = $line.Split("|")
        $movieById[$tokens[0]] = $tokens[1]
    }
    # Load movies user has already seen (rated)
    # into a hash table
    Write-Host "Reading rated movies" -ForegroundColor Green
    $ratedMovieIds = @{}
    foreach($line in Get-Content $userDataFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            # Resolve the ID to the movie name
            $ratedMovieIds[$movieById[$tokens[1]]] = $tokens[2]
        }
    }
    # Read recommendations generated by Mahout
    Write-Host "Reading recommendations" -ForegroundColor Green
    $recommendations = @{}
    foreach($line in get-content $recommendationFile)
    {
        $tokens = $line.Split("`t")
        if($tokens[0] -eq $userId)
        {
            #Trim leading/treailing [] and split at ,
            $movieIdAndScores = $tokens[1].TrimStart("[").TrimEnd("]").Split(",")
            foreach($movieIdAndScore in $movieIdAndScores)
            {
                #Split at : and store title and score in a hash table
                $idAndScore = $movieIdAndScore.Split(":")
                $recommendations[$movieById[$idAndScore[0]]] = $idAndScore[1]
            }
            break
        }
    }

    Write-Host "Rated movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $ratedFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                   @{Expression={$_.Value};Label="Rating"}
    $ratedMovieIds | format-table $ratedFormat
    Write-Host "---------------------------" -ForegroundColor Green

    write-host "Recommended movies" -ForegroundColor Green
    Write-Host "---------------------------" -ForegroundColor Green
    $recommendationFormat = @{Expression={$_.Name};Label="Movie";Width=40}, `
                            @{Expression={$_.Value};Label="Score"}
    $recommendations | format-table $recommendationFormat

Para usar este script, debe tener previamente extraída la carpeta **ml-100k**, así como contar con una copia local del archivo de salida **part-r-00000** generado por el trabajo de Mahout. A continuación se muestra un ejemplo de la ejecución del script.

    PS C:PS C:\> show-recommendation.ps1 -userId 4 -userDataFile .\ml-100k\u.data -movieFile .\ml-100k\u.item -recommendationFile .\output.txtgt; show-recommendation.ps1 -userId 4 -userDataFile .\ml-100k\u.data -movieFile .\ml-100k\u.item -recommendationFile .\output.txt

> [WACOM.NOTE] El script de Python de ejemplo, **show\_recommendations.py**, toma los mismos parámetros.

La salida debe ser similar a la siguiente:

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237  

## <a name="classify"></a>Clasificación de datos mediante la línea de comandos de Hadoop

Uno de los métodos de clasificación disponibles con Mahout es compilar un [bosque aleatorio][bosque aleatorio]. Se trata de un proceso de varios pasos que supone el uso de datos de aprendizaje para generar un árbol de decisiones, que luego se usará para clasificar los datos. Para ello se utiliza la clase **org.apache.mahout.classifier.df.tools.Describe** que se proporciona en Mahout, y actualmente se debe ejecutar mediante la línea de comandos de Hadoop.

### Carga de los datos

La implementación actual de Mahout es compatible con el formato de repositorio de la Universidad de California, Irvine (UCI).

1.  Descargue los siguientes archivos desde <http://nsl.cs.unb.ca/NSL-KDD/>.

-   [KDDTrain+.ARFF][KDDTrain+.ARFF]: el archivo del curso

-   [KDDTest+.ARFF][KDDTest+.ARFF]: los datos de prueba

1.  Abra cada uno de los archivos y elimine las líneas de la parte superior que comienzan por '@'. Luego, guarde los archivos. Si no las elimina, recibirá errores cuando use estos datos con Mahout.

2.  Cargue los archivos en **example/data**. Para ello, puede usar la función `Add-HDInsightFile` del módulo de PowerShell [HDInsight-Tools][HDInsight-Tools].

### Ejecución del trabajo

1.  Puesto que este trabajo requiere la línea de comandos de Hadoop, primero debe habilitar Escritorio remoto a través del [Portal de administración de Azure][Portal de administración de Azure]. En el portal, seleccione el clúster de HDInsight y luego seleccione **Habilitar de forma remota** en la parte inferior de la página **Configuración**.

    ![enable remote][enable remote]

    Cuando se le pida, escriba un nombre de usuario y una contraseña para usar en las sesiones remotas.

2.  Una vez habilitado el acceso remoto, seleccione **Conectar** para comenzar la conexión. Esta acción descargará un archivo **.rdp**, que se puede usar para iniciar una sesión de Escritorio remoto.

    ![connect][connect]

3.  Después de conectar, use el icono de **línea de comandos de Hadoop** para abrir la línea de comandos de Hadoop.

    ![hadoop cli][hadoop cli]

4.  Use el siguiente comando para generar el descriptor de archivos (**KDDTrain+.info**), mediante Mahout.

        hadoop jar "c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar" org.apache.mahout.classifier.df.tools.Describe -p "wasb:///example/data/KDDTrain+.arff" -f "wasb:///example/data/KDDTrain+.info" -d N 3 C 2 N C 4 N C 8 N 2 C 19 N L

    El código `N 3 C 2 N C 4 N C 8 N 2 C 19 N L` describe los atributos de los datos en el archivo. 1 atributo numérico, 2 categórico, etc. L indica una etiqueta (label).

5.  Compile un bosque de árboles de decisiones mediante el siguiente comando.

        hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.BuildForest -Dmapred.max.split.size=1874231 -d wasb:///example/data/KDDTrain+.arff -ds wasb:///example/data/KDDTrain+.info -sl 5 -p -t 100 -o nsl-forest

    La salida de esta operación se almacena en el directorio **nsl-forest**, que está ubicado en el almacenamiento del clúster de HDInsight en \_\_wasb://user/\<nombre de usuario\>/nsl-forest/nsl-forest.seq. El \<nombre de usuario\> es el nombre de usuario usado en la sesión de escritorio remoto. Este archivo no es legible para el ojo humano.

6.  Pruebe el bosque clasificando el conjunto de datos **KDDTest+.arff** con el siguiente comando.

        hadoop jar c:/apps/dist/mahout-0.9.0.2.1.3.0-1887/examples/target/mahout-examples-0.9.0.2.1.3.0-1887-job.jar org.apache.mahout.classifier.df.mapreduce.TestForest -i wasb:///example/data/KDDTest+.arff -ds wasb:///example/data/KDDTrain+.info -m nsl-forest -a -mr -o wasb:///example/data/predictions

    Este comando devolverá información resumida sobre el proceso de clasificación parecida a la siguiente.

        14/07/02 14:29:28 INFO mapreduce.TestForest:

        =======================================================
        Summary
        -------------------------------------------------------
        Correctly Classified Instances          :      17560       77.8921%
        Incorrectly Classified Instances        :       4984       22.1079%
        Total Classified Instances              :      22544

        =======================================================
        Confusion Matrix
        -------------------------------------------------------
        a       b       <--Classified as
        9437    274      |  9711        a     = normal
        4710    8123     |  12833       b     = anomaly

        =======================================================
        Statistics
        -------------------------------------------------------
        Kappa                                       0.5728
        Accuracy                                   77.8921%
        Reliability                                53.4921%
        Reliability (standard deviation)            0.4933

Este trabajo produce también un archivo ubicado en **wasb:///example/data/predictions/KDDTest+.arff.out**, si bien este archivo no es legible para el ojo humano.

> [WACOM.NOTE] Los trabajos de Mahout no sobrescriben archivos. Si desea ejecutar estos trabajos de nuevo, debe eliminar los archivos creados por trabajos anteriores.

## <a name="troubleshooting"></a> Solución de problemas

### <a name="install"></a>Instalación de Mahout

Mahout se instala en clústeres de HDInsight 3.1, y se puede instalar manualmente en clústeres 3.0 o 2.1 siguiendo estos pasos.

1.  La versión de Mahout que utilice depende de la versión de HDInsight del clúster. Para encontrar la versión del clúster, use el siguiente código con [Azure PowerShell][Azure PowerShell]:

        PS C:PS C:\> Get-AzureHDInsightCluster -Name YourClusterName | Select versiongt; Get-AzureHDInsightCluster -Name YourClusterName | Select version

-   **Para HDInsight 2.1**, puede descargar un archivo jar que contiene [Mahout 0.9][Mahout 0.9].

-   **Para HDInsight 3.0**, debe [compilar Mahout a partir del código fuente][compilar Mahout a partir del código fuente] y especificar la versión de Hadoop proporcionada por HDInsight. Instale los requisitos previos que aparecen en la página de compilación, descargue el código fuente y use luego el siguiente comando para crear los archivos jar de Mahout.

            mvn -Dhadoop2.version=2.2.0 -DskipTests clean package

        Once the build completes, the jar file will be created at __mahout\mrlegacy\target\mahout-mrlegacy-1.0-SNAPSHOT-job.jar__.

        > [WACOM.NOTE] Once Mahout 1.0 is released, you should be able to use the pre-built packages with HDInsight 3.0.

1.  Cargue el archivo jar en **example/jars** en el almacenamiento predeterminado del clúster. En el ejemplo siguiente se usa el script [send-hdinsight][sendhdinsight] para cargar el archivo.

        PS C:PS C:\> .\Send-HDInsight -LocalPath "path\to\mahout-core-0.9-job.jar" -DestinationPath "example/jars/mahout-core-0.9-job.jar" -ClusterName "your cluster name"gt; .\Send-HDInsight -LocalPath "path\to\mahout-core-0.9-job.jar" -DestinationPath "example/jars/mahout-core-0.9-job.jar" -ClusterName "your cluster name"

### No se pueden sobrescribir los archivos

Los trabajos de Mahout no limpian los archivos temporales creados durante el procesamiento. Además, los trabajos no sobrescriben los archivos de salida existentes.

Para evitar errores al ejecutar trabajos de Mahout, elimine los archivos temporales y de salida entre una ejecución y otra, o use nombres de directorio temporal y de salida exclusivos.

### No se encuentra el archivo jar

Aunque HDInsight 3.1 incluye Mahout, la ruta y el nombre de archivo incluyen el número de la versión de Mahout instalada en el clúster. El script de ejemplo de PowerShell de este tutorial usa una ruta que es válida a partir de julio de 2014, pero el número de la versión cambiará en futuras actualizaciones de HDInsight. Para determinar la ruta actual al archivo jar de Mahout de su clúster, use los siguientes comandos de PowerShell y luego modifique el script para que haga referencia a la ruta de archivo devuelta.

    Use-AzureHDInsightCluster -Name $clusterName
    $jarFile = Invoke-Hive -Query '!${env:COMSPEC} /c dir /b /s ${env:MAHOUT_HOME}\examples\target\*-job.jar'

### <a name="nopowershell"></a>Clases que no funcionan con PowerShell

Los trabajos de Mahout que usan las siguientes clases devolverán diversos errores si se utilizan desde PowerShell.

-   org.apache.mahout.utils.clustering.ClusterDumper
-   org.apache.mahout.utils.SequenceFileDumper
-   org.apache.mahout.utils.vectors.lucene.Driver
-   org.apache.mahout.utils.vectors.arff.Driver
-   org.apache.mahout.text.WikipediaToSequenceFile
-   org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
-   org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
-   org.apache.mahout.classifier.sgd.TrainLogistic
-   org.apache.mahout.classifier.sgd.RunLogistic
-   org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
-   org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
-   org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
-   org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
-   org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
-   org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
-   org.apache.mahout.classifier.df.tools.Describe

Para ejecutar los trabajos que usan estas clases, conéctese al clúster de HDInsight y utilice la línea de comandos de Hadoop. Consulte [Clasificación de datos mediante la línea de comandos de Hadoop][Clasificación de datos mediante la línea de comandos de Hadoop] para ver un ejemplo.

  [Apache Mahout]: http://mahout.apache.org
  [Introducción al uso de HDInsight de Azure]: http://azure.microsoft.com/es-es/documentation/articles/hdinsight-get-started/
  [Instalación de Mahout]: #install
  [aprendizaje automático]: http://en.wikipedia.org/wiki/Machine_learning
  [Generación de recomendaciones mediante PowerShell]: #recommendations
  [Clasificación de datos mediante la línea de comandos de Hadoop]: #classify
  [Solución de problemas]: #troubleshooting
  [calificaciones de películas]: http://grouplens.org/datasets/movielens/
  [MovieLens 100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
  [Azure PowerShell]: http://azure.microsoft.com/es-es/documentation/articles/install-configure-powershell/
  [HDInsight-Tools]: https://github.com/Blackmist/hdinsight-tools
  [Carga de datos para trabajos de Hadoop en HDInsight]: http://azure.microsoft.com/es-es/documentation/articles/hdinsight-upload-data/
  [bosque aleatorio]: http://en.wikipedia.org/wiki/Random_forest
  [KDDTrain+.ARFF]: http://nsl.cs.unb.ca/NSL-KDD/KDDTrain+.arff
  [KDDTest+.ARFF]: http://nsl.cs.unb.ca/NSL-KDD/KDDTest+.arff
  [Portal de administración de Azure]: https://manage.windowsazure.com/
  [enable remote]: ./media/hdinsight-mahout/enableremote.png
  [connect]: ./media/hdinsight-mahout/connect.png
  [hadoop cli]: ./media/hdinsight-mahout/hadoopcli.png
  [Mahout 0.9]: http://repo2.maven.org/maven2/org/apache/mahout/mahout-core/0.9/mahout-core-0.9-job.jar
  [compilar Mahout a partir del código fuente]: http://mahout.apache.org/developers/buildingmahout.html
