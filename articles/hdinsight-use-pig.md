<properties linkid="manage-services-hdinsight-howto-pig" urlDisplayName="Use Pig with HDInsight" pageTitle="Use Pig with HDInsight | Azure" metaKeywords="" description="Learn how to use Pig with HDInsight. Write Pig Latin statements to analyze an application log file, and run queries on the data to generate output for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Pig with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Uso de Pig con HDInsight
========================

[Apache *Pig*](http://pig.apache.org/) proporciona un lenguaje de scripting para ejecutar trabajos de *MapReduce* como alternativa a la escritura de código Java. En este tutorial, usará PowerShell para ejecutar algunas instrucciones de Pig Latin a fin de analizar un archivo de registro log4j de Apache y ejecutar varias consultas en los datos para generar una salida. En este tutorial se muestran las ventajas de Pig y cómo se puede usar para simplificar los trabajos de MapReduce.

El lenguaje de scripting de Pig se llama *Pig Latin*. Las instrucciones de Pig Latin siguen este flujo general:

-   **Carga**: Datos leídos que se van a manipular desde el sistema de archivos.
-   **Transformación**: Manipulación de los datos.
-   **Volcado o almacenamiento**: Datos de salida en la pantalla o almacenamiento para su procesamiento.

Para obtener más información acerca de Pig Latin, consulte el [manual de referencia de Pig Latin 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) (en inglés) y el [manual de referencia de Pig Latin 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html) (en inglés).

**Requisitos previos**

Tenga en cuenta los siguientes requisitos antes de empezar este artículo:

-   Un clúster de HDInsight de Azure. Para obtener instrucciones, consulte [Introducción a HDInsight de Azure](/es-es/manage/services/hdinsight/get-started-hdinsight/) o [Aprovisionamiento de clústeres de HDInsight](/es-es/manage/services/hdinsight/provision-hdinsight-clusters/).
-   Instale y configure Azure PowerShell. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](/es-es/documentation/articles/install-configure-powershell/).

**Duración aproximada:** 30 minutos

En este artículo
----------------

-   [Caso de uso de Pig](#usage)
-   [Carga de archivos de datos al almacenamiento de blobs de Azure](#uploaddata)
-   [Descripción de Pig Latin](#understand)
-   [Ejecución de Pig Latin usando PowerShell](#powershell)
-   [Pasos siguientes](#nextsteps)

Caso de uso de Pig
------------------

Las bases de datos son ideales para pequeños conjuntos de datos y consultas de baja latencia. Sin embargo, cuando nos enfrentamos a grandes conjuntos que contienen terabytes de datos, las bases de datos SQL tradicionales no son la solución ideal. Desde siempre, los administradores de bases de datos han tenido que adquirir hardware de grandes dimensiones a medida que la carga de las bases de datos se incrementaba y el rendimiento disminuía.

Por lo general, todas las aplicaciones guardan errores, excepciones y otros problemas codificados en un archivo de registro para que los administradores puedan revisarlos o generar determinadas métricas a partir de los datos de dicho archivo de registro. Estos archivos de registro normalmente adquieren grandes tamaños y contienen una gran cantidad de datos que deben procesarse y extraerse.

Por lo tanto, los archivos de registro son buenos ejemplos de datos de gran tamaño. Trabajar con datos de gran tamaño es difícil si se usan bases de datos relacionales y paquetes de estadísticas/visualización. Debido a las grandes cantidades de datos y el cálculo de los mismos, a menudo es necesario tener software que se ejecute en paralelo en decenas, cientos o incluso miles de servidores para calcular estos datos en un plazo razonable. Hadoop proporciona un marco de MapReduce para escribir aplicaciones que procesan grandes cantidades de datos estructurados y sin estructurar en paralelo en grandes clústeres de máquinas de forma muy fiable y con tolerancia a errores.

Usar Pig reduce el tiempo necesario para escribir programas de asignador y reductor. Es decir, no es necesario usar Java ni código reutilizable. Además, tiene la flexibilidad de combinar el código Java con Pig. Se pueden escribir muchos algoritmos complejos en menos de cinco líneas de código Pig legible para el ojo humano.

La representación visual de lo que conseguirá en este artículo se muestra en las dos ilustraciones siguientes. Estas ilustraciones muestran un ejemplo representativo del conjunto de datos para ilustrar el flujo y la transformación de los datos a medida que revisa las líneas de código Pig del script. La primera ilustración muestra un ejemplo del archivo log4j:

![Ejemplo del archivo completo](./media/hdinsight-use-pig/HDI.wholesamplefile.png)

La segunda ilustración muestra la transformación de los datos:

![HDI.PIG.Data.Transformation](./media/hdinsight-use-pig/HDI.DataTransformation.gif)

Carga de archivos de datos al almacenamiento de blobs
-----------------------------------------------------

HDInsight utiliza contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](/es-es/manage/services/hdinsight/howto-blob-store/). En este artículo, usará un archivo log4j de muestra distribuido con el clúster de HDInsight que se almacena en *\\example\\data\\sample.log*. Para obtener información acerca de cómo cargar los datos, consulte [Carga de datos en HDInsight](/es-es/manage/services/hdinsight/howto-upload-data-to-hdinsight/).

Para acceder a los archivos, use la sintaxis siguiente:

     wasb[s]://[<containerName>@<storageAccountName>.blob.core.windows.net]/<path>/<filename>

Por ejemplo:

     wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

Reemplace *mycontainer* por el nombre del contenedor y *mystorage* por el nombre de la cuenta de almacenamiento de blobs.

Como el archivo se almacena en el sistema de archivos predeterminado, también puede acceder al archivo usando lo siguiente:

     wasb:///example/data/sample.log
        /example/data/sample.log

Descripción de Pig Latin
------------------------

En esta sesión repasará algunas de las instrucciones de Pig Latin y los resultados de ejecutar las instrucciones. Y en la siguiente, utilizará PowerShell para ejecutar las instrucciones de Pig.

1.  Carga de datos desde el sistema de archivos y muestra de los resultados

         LOGS = LOAD 'wasb:///example/data/sample.log';
         DUMP LOGS;

    La salida será similar a la siguiente:

         (2012-02-05 19:23:50 SampleClass5 [TRACE] verbose detail for id 313393809)
         (2012-02-05 19:23:50 SampleClass6 [DEBUG] detail for id 536603383)
         (2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail for id 564842645)
         (2012-02-05 19:23:50 SampleClass8 [TRACE] verbose detail for id 1929822199)
         (2012-02-05 19:23:50 SampleClass5 [DEBUG] detail for id 1599724386)
         (2012-02-05 19:23:50 SampleClass0 [INFO] everything normal for id 2047808796)
         (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1774407365)
         (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 2099982986)
         (2012-02-05 19:23:50 SampleClass4 [DEBUG] detail for id 180683124)
         (2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1072988373)
         (2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail)
         ...

2.  Revise todas las línea del archivo de datos para buscar una coincidencia en los seis niveles de registro:

         LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

3.  Filtre las filas que no tengan una coincidencia. Por ejemplo, las filas vacías.

         FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
         DUMP FILTEREDLEVELS;

    La salida será similar a la siguiente:

         (DEBUG)
         (TRACE)
         (TRACE)
         (DEBUG)
         (TRACE)
         (TRACE)
         (DEBUG)
         (TRACE)
         (TRACE)
         (DEBUG)
         (TRACE)
         ...

4.  Agrupe todos los niveles de registro en su fila correspondiente:

         GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
         DUMP GROUPEDLEVELS;

    La salida será similar a la siguiente:

         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
         (TRACE), ...

5.  En cada grupo, cuente las repeticiones de los niveles de registro. Esta será la frecuencia de cada nivel de registro:

         FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
         DUMP FREQUENCIES;

    La salida será similar a la siguiente:

         (INFO,3355)
         (WARN,361)
         (DEBUG,15608)
         (ERROR,181)
         (FATAL,37)
         (TRACE,29950)

6.  Ordene las frecuencias en orden descendente:

         RESULT = order FREQUENCIES by COUNT desc;
         DUMP RESULT;   

    La salida será similar a la siguiente:

         (TRACE,29950)
         (DEBUG,15608)
         (INFO,3355)
         (WARN,361)

    (ERROR,181)

         (FATAL,37)

Ejecución de Pig Latin usando PowerShell
----------------------------------------

En esta sección se proporcionan las instrucciones para usar los cmdlets de PowerShell. Antes de pasar a esta sección, debe configurar el entorno local y la conexión con Azure. Para obtener información, consulte [Introducción a HDInsight de Azure](/es-es/manage/services/hdinsight/get-started-hdinsight/) o [Administración de HDInsight con PowerShell](/en-use/manage/services/hdinsight/administer-hdinsight-using-powershell/).

**Para ejecutar Pig Latin usando PowerShell**

1.  Abra una ventana de la consola de Azure PowerShell. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](/es-es/documentation/articles/install-configure-powershell/).
2.  Ejecute el comando siguiente para conectarse a su suscripción de Azure:

         Add-AzureAccount

    Se le pedirá que introduzca las credenciales de su cuenta de Azure.

3.  Configure la variable del script siguiente y ejecútelo:

         $clusterName = "<HDInsightClusterName>" 

4.  Ejecute los comandos siguientes para definir la cadena de consulta de Pig Latin:

         # Crear una definición de trabajo de Pig.
         $0 = '$0';
         $QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                         "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                         "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                         "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                         "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                         "RESULT = order FREQUENCIES by COUNT desc;" +
                         "DUMP RESULT;" 
            
         $pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString 

    También usará el modificador -File para especificar un archivo de script de Pig en HDFS.

5.  Ejecute el script siguiente para enviar el trabajo de Pig:

         # Enviar el trabajo de Pig.
         Select-AzureSubscription $subscriptionName
         $pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition 

6.  Ejecute el script siguiente para esperar a que el trabajo de Pig termine:

         # Esperar hasta que se haya completado el trabajo de Pig.
         Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

7.  Ejecute el script siguiente para imprimir la salida del trabajo de Pig:

         # Imprimir el error estándar y la salida estándar del trabajo de Pig.
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

    ![HDI.Pig.PowerShell](./media/hdinsight-use-pig/hdi.pig.powershell.png)

    El trabajo de Pig calcula las frecuencias de los diferentes tipos de registro.

Pasos siguientes
----------------

Aunque Pig le permite realizar análisis de datos, también hay otros lenguajes incluidos con HDInsight que podrían resultarle interesantes. Hive proporciona un lenguaje de consulta tipo SQL que le permite realizar consultas fácilmente en los datos almacenados en HDInsight, mientras que los trabajos de MapReduce escritos en Java le permiten realizar análisis de datos complejos. Para obtener más información, consulte los temas siguientes:

-   [Introducción a HDInsight de Azure](/es-es/manage/services/hdinsight/get-started-hdinsight/)
-   [Carga de datos en HDInsight](/es-es/manage/services/hdinsight/howto-upload-data-to-hdinsight/)
-   [Uso de Hive con HDInsight](/es-es/manage/services/hdinsight/using-hive-with-hdinsight/)

