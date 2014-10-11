<properties linkid="manage-services-hdinsight-howto-hive" urlDisplayName="Use Hadoop Hive in HDInsight" pageTitle="Use Hadoop Hive in HDInsight | Azure" metaKeywords="" description="Learn how to use Hive with HDInsight. You'll use a log file as input into an HDInsight table, and use HiveQL to query the data and report basic statistics." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop Hive in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Uso de Hive con Hadoop en HDInsight

[Apache Hive][] ofrece el modo de ejecutar un trabajo de MapReduce mediante un lenguaje de scripting de tipo SQL, llamado *HiveQL*, que se puede aplicar al resumen, la consulta y el análisis de grandes volúmenes de datos. En este artículo, utilizará HiveQL para consultar los datos de un archivo de registro log4j de Apache y elaborar las estadísticas básicas.

**Requisitos previos:**

-   Debe aprovisionar un **clúster de HDInsight**. Para saber cómo hacerlo con el Portal de Azure, consulte [Introducción a HDInsight][]. Para obtener instrucciones acerca de otras formas de creación de dichos clústeres, consulte [Aprovisionamiento de clústeres de HDInsight][].

-   Debe tener instalado **Azure PowerShell** en su estación de trabajo. Para obtener instrucciones acerca de cómo hacerlo, consulte [Instalación y configuración de Azure PowerShell][].

**Duración aproximada:** 30 minutos

## En este artículo

-   [Caso de uso de Hive][]
-   [Carga de archivos de datos al almacenamiento de blobs de Azure][]
-   [Ejecución de las consultas de Hive usando PowerShell][]
-   [Pasos siguientes][]

## <span id="usage"></span></a>Caso de uso de Hive

Las bases de datos son adecuadas para administrar conjuntos pequeños de datos que admiten consultas de baja latencia. No obstante, si se trata de grandes conjuntos de datos que contienen terabytes de datos, las bases de datos SQL tradicionales no son por lo general la solución ideal. Cada vez con más frecuencia los administradores de las bases de datos se enfrentan a estos grandes conjuntos de datos; y compran hardware más voluminoso a medida que crece la carga de la base de datos y disminuye el rendimiento.

Hive soluciona los problemas asociados a las grandes cantidades de datos permitiendo a los usuarios escalar horizontalmente cuando realizan consultas en grandes conjuntos de datos. Hive consulta los datos en paralelo entre varios nodos que usan MapReduce distribuyendo la base de datos entre un número cada vez mayor de hosts a medida que la carga aumenta.

Hive y HiveQL ofrecen además una alternativa a la escritura de trabajos de MapReduce en Java al realizar consultas en los datos. Proporciona un sencillo contenedor de tipo SQL que permite que las consultas se escriban en HiveQL. A continuación, HDInsight las compila en MapReduce y las ejecuta en el clúster.

Hive además permite que los programadores familiarizados con el marco de MapReduce conecten mapeadores y reductores personalizados para realizar un análisis más sofisticado que podría no ser compatible con las capacidades integradas del lenguaje de HiveQL.

Hive está destinado al procesamiento de lotes de grandes cantidades de datos inmutables (como blogs). No es adecuado para aplicaciones a transacciones que necesitan tiempos de respuesta rápidos, como los sistemas de administración de bases de datos. Hive se ha optimizado para la escalabilidad (se pueden añadir dinámicamente más máquinas al clúster de Hadoop), la extensibilidad (dentro del marco de MapReduce y con otras interfaces de programación) y la tolerancia a errores. La latencia no es una consideración clave del diseño.

Normalmente, las aplicaciones guardan errores, excepciones y otros problemas de código en un archivo de registro, para que los administradores puedan usar los datos de estos archivos de registro a fin de revisar los problemas que pueden surgir y para generar métricas relevantes a los errores u otros problemas, como los de rendimiento. Estos archivos de registro normalmente adquieren grandes tamaños y contienen una gran cantidad de datos que deben procesarse y extraerse para ofrecer inteligencia en la aplicación.

Por lo tanto, los archivos de registro son ejemplos de grandes cantidades de datos. HDInsight proporciona un sistema de almacén de datos de Hive que facilita un sencillo resumen de los datos, consultas ad hoc y el análisis de estos grandes conjuntos de datos en sistemas de archivos compatibles con Hadoop, como el almacenamiento de blobs de Azure.

## <span id="uploaddata"></span></a>Carga de archivos de datos al almacenamiento de blobs

HDInsight utiliza el contenedor de almacenamiento de blobs de Azure como sistema de archivos predeterminado. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][].

En este artículo, usará un archivo log4j de muestra distribuido con el clúster de HDInsight que se almacena en *\\example\\data\\sample.log*. Cada registro del archivo consta de una línea de campos que contiene un campo `[LOG LEVEL]` para mostrar el tipo y la gravedad. Por ejemplo:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937 

Para acceder a los archivos, use la sintaxis siguiente:

    wasb://<containerName>@<AzureStorageName>.blob.core.windows.net

Por ejemplo:

    wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

Reemplace *mycontainer* por el nombre del contenedor y *mystorage* por el nombre de la cuenta de almacenamiento de blobs.

Como el archivo se almacena en el sistema de archivos predeterminado, también puede acceder al archivo usando lo siguiente:

    wasb:///example/data/sample.log
    /example/data/sample.log

Para generar sus propios archivos log4j, use la utilidad de registro [Apache Log4j][]. Para obtener información acerca de cómo cargar datos al almacenamiento de blobs de Azure, consulte [Carga de datos en HDInsight][].

## <span id="runhivequeries"></span></a> Ejecución de las consultas de Hive usando PowerShell

En la sección anterior, ha cargado un archivo log4j llamado sample.log al contendor del sistema de archivos predeterminado. Y en esta, ejecutará HiveQL para crear una tabla de Hive, cargar datos en ella y, finalmente, realizar consultas en los datos para determinar cuántos registros de error había.

Este artículo proporciona las instrucciones para usar los cmdlets de Azure PowerShell para ejecutar una consulta de Hive. Antes de comenzar con esta sección, debe configurar el entorno local y la conexión a Azure como se indica en la sección de **Requisitos previos** al inicio de este tema.

Las consultas de Hive se pueden ejecutar en PowerShell usando el cmdlet **Start-AzureHDInsightJob** o **Invoke-Hive**.

**Para ejecutar las consultas de Hive usando Start-AzureHDInsightJob**

1.  Abra una ventana de la consola de Azure PowerShell. Las instrucciones se encuentran en [Instalación y configuración de Azure PowerShell][].
2.  Ejecute el comando siguiente para conectarse a su suscripción de Azure:

        Add-AzureAccount

    Se le pedirá que escriba las credenciales de la cuenta de Azure.

3.  Configure las variables en el script siguiente y ejecútelo:

        # Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide HDInsight cluster name Where you want to run the Hive job
        $clusterName = "<HDInsightClusterName>"

4.  Ejecute el script siguiente para definir las consultas de HiveQL:

        # HiveQL queries
        # Use the internal table option. 
        $queryString = "DROP TABLE log4jLogs;" +
                       "CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ';" +
                       "LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/sample.log' OVERWRITE INTO TABLE log4jLogs;" +
                       "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

        # Use the external table option. 
        $queryString = "DROP TABLE log4jLogs;" +
                        "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/';" +
                        "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

    El comando LOAD DATA de HiveQL hará que se mueva el archivo de datos a la carpeta \\hive\\warehouse\\. El comando DROP TABLE eliminará la tabla y el archivo de datos. Si utiliza la opción de tabla interna y desea volver a ejecutar el script, debe cargar el archivo .log de muestra de nuevo. Si desea conservar el archivo de datos, debe usar el comando CREATE EXTERNAL TABLE como se indica en el script.

    También puede usar la tabla externa para cuando el archivo de datos se ubique en un contenedor o una cuenta de almacenamiento diferentes.

    Use DROP TABLE primero en caso de que ejecute el script de nuevo y que la tabla de log4jlogs ya exista.

5.  Ejecute el script siguiente para crear una definición de trabajo de Hive:

        # Create a Hive job definition 
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString 

    También usará el conmutador -File para especificar un archivo de script de HiveQL en HDFS.

6.  Ejecute el script siguiente para enviar un trabajo de Hive:

        # Submit the job to the cluster 
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7.  Ejecute el script siguiente para esperar a que el trabajo de Hive termine:

        # Wait for the Hive job to complete
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8.  Ejecute el script siguiente para imprimir la salida estándar:

        # Print the standard error and the standard output of the Hive job.
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

    ![HDI.HIVE.PowerShell][]

    El resultado es:

        [ERROR] 3

**Para enviar las consultas de Hive usando Invoke-Hive**

1.  Abra una ventana de la consola de Azure PowerShell.
2.  Ejecute el comando siguiente para conectarse a su suscripción de Azure:

        Add-AzureAccount

    Se le pedirá que escriba las credenciales de la cuenta de Azure.

3.  Configure la variable y, a continuación, ejecútela:

        $clusterName = "<HDInsightClusterName>"

4.  Ejecute el script siguiente para invocar las consultas de HiveQL:

        Use-AzureHDInsightCluster $clusterName 
        $response = Invoke-Hive -Query @"
            SELECT * FROM hivesampletable
                WHERE devicemake LIKE "HTC%"
                LIMIT 10; 
        "@

        Write-Host $response

    La salida es la siguiente:

    ![PowerShell Invoke-Hive output][]

    Para consultas de HiveQL más extensas, se recomienda usar PowerShell Here-Strings o el archivo de script de HiveQL. Los ejemplos siguientes indican cómo usar el cmdlet Invoke-Hive para ejecutar un archivo de script de HiveQL. El archivo de script de HiveQL debe cargarse en WASB.

        Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"

    Para obtener más información acerca de Here-Strings, consulte [Uso de Windows PowerShell Here-Strings][].

## <span id="nextsteps"></span></a>Pasos siguientes

Aunque Hive facilita la realización de las consultas de datos usando un lenguaje de consultas de tipo SQL, otros componentes disponibles con HDInsight proporcionan funciones complementarias, como el movimiento y la transformación de los datos. Para obtener más información, consulte los artículos siguientes:

-   [Introducción a HDInsight de Azure][Introducción a HDInsight]
-   [Análisis de la información de retraso de vuelos usando HDInsight][]
-   [Uso de Oozie con HDInsight][]
-   [Envío de trabajos de Hadoop mediante programación][]
-   [Carga de datos en HDInsight][]
-   [Uso de Pig con HDInsight][]
-   [Azure HDInsight SDK documentation][]

  [Apache Hive]: http://hive.apache.org/
  [Introducción a HDInsight]: ../hdinsight-get-started/
  [Aprovisionamiento de clústeres de HDInsight]: ../hdinsight-provision-clusters/
  [Instalación y configuración de Azure PowerShell]: ../install-configure-powershell/
  [Caso de uso de Hive]: #usage
  [Carga de archivos de datos al almacenamiento de blobs de Azure]: #uploaddata
  [Ejecución de las consultas de Hive usando PowerShell]: #runhivequeries
  [Pasos siguientes]: #nextsteps
  [Uso del almacenamiento de blobs de Azure con HDInsight]: ../hdinsight-use-blob-storage
  [Apache Log4j]: http://en.wikipedia.org/wiki/Log4j
  [Carga de datos en HDInsight]: ../hdinsight-upload-data/
  [HDI.HIVE.PowerShell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
  [PowerShell Invoke-Hive output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
  [Uso de Windows PowerShell Here-Strings]: http://technet.microsoft.com/en-us/library/ee692792.aspx
  [Análisis de la información de retraso de vuelos usando HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Uso de Oozie con HDInsight]: ../hdinsight-use-oozie/
  [Envío de trabajos de Hadoop mediante programación]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Uso de Pig con HDInsight]: ../hdinsight-use-pig/
  [Azure HDInsight SDK documentation]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx
