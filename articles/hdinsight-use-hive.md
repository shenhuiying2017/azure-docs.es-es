<properties urlDisplayName="Use Hadoop Hive in HDInsight" pageTitle="Uso de Hive de Hadoop en HDInsight | Azure" metaKeywords="" description="Aprenda a usar Hive con HDInsight. Utilizar&aacute; un archivo de registro como entrada en una tabla de HDInsight, adem&aacute;s de usar HiveQL para consultar los datos y elaborar las estad&iacute;sticas b&aacute;sicas." metaCanonical="" services="hdinsight" documentationCenter="" title="Uso de Hive de Hadoop en HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# Uso de Hive con Hadoop en HDInsight

[Apache Hive][Apache Hive] ofrece la posibilidad de ejecutar un trabajo de MapReduce a través de un lenguaje de scripting similar a SQL, denominado *HiveQL*. Hive es un sistema de almacén de datos para Hadoop, que permite realizar resúmenes de datos, consultas y análisis de grandes volúmenes de datos. En este artículo, utilizará HiveQL para consultar un archivo de datos de ejemplo que se incluye como parte del aprovisionamiento de clústeres de HDInsight.

**Requisitos previos:**

-   Debe aprovisionar un **clúster de HDInsight**. Para saber cómo hacerlo con el Portal de Azure, consulte [Introducción a HDInsight][Introducción a HDInsight]. Para obtener instrucciones acerca de otras formas de creación de dichos clústeres, consulte [Aprovisionamiento de clústeres de HDInsight][Aprovisionamiento de clústeres de HDInsight].

-   Debe tener instalado **Azure PowerShell** en su estación de trabajo. Para obtener instrucciones acerca de cómo hacerlo, consulte [Instalación y configuración de Azure PowerShell][Instalación y configuración de Azure PowerShell].

**Duración aproximada:** 30 minutos

## En este artículo

-   [Caso de uso de Hive][Caso de uso de Hive]
-   [Carga de datos en tablas de Hive][Carga de datos en tablas de Hive]
-   [Ejecución de las consultas de Hive usando PowerShell][Ejecución de las consultas de Hive usando PowerShell]
-   [Uso de Tez para un mejor rendimiento][Uso de Tez para un mejor rendimiento]
-   [Pasos siguientes][Pasos siguientes]

## <span id="usage"></span></a>Caso de uso de Hive

![HDI.HIVE.Architecture][HDI.HIVE.Architecture]

Hive proyecta un cierto tipo de estructura en un conjunto de datos esencialmente no estructurados (que es de lo que se trata Hadoop) y luego le permite que consulte los datos con HiveQL. Hive proporciona una capa de abstracción sobre el marco de MapReduce basado en Java, que permite a los usuarios consultar datos sin conocimiento de Java o de MapReduce. HiveQL proporciona un sencillo contenedor de tipo SQL que permite que las consultas se escriban en HiveQL. A continuación, HDInsight las compila en MapReduce y las ejecuta en el clúster. Otras ventajas de Hive son las siguientes:

-   Hive permite que los programadores familiarizados con el marco de MapReduce conecten asignadores y reductores personalizados para realizar un análisis más sofisticado que podría no ser compatible con las capacidades integradas del lenguaje HiveQL.
-   Hive está destinado al procesamiento de lotes de grandes cantidades de datos inmutables (como blogs). No es adecuado para aplicaciones a transacciones que necesitan tiempos de respuesta rápidos, como los sistemas de administración de bases de datos.
-   Hive se ha optimizado para la escalabilidad (se pueden añadir dinámicamente más máquinas al clúster de Hadoop), la extensibilidad (dentro del marco de MapReduce y con otras interfaces de programación) y la tolerancia a errores. La latencia no es una consideración clave del diseño.

## <span id="uploaddata"></span></a>Carga de datos en tablas de Hive

HDInsight utiliza el contenedor de almacenamiento de blobs de Azure como sistema de archivos predeterminado para clústeres de Hadoop. Se agregan algunos archivos de datos de ejemplo al almacenamiento de blobs como parte del aprovisionamiento de clústeres. Puede usar estos archivos de datos de ejemplo para ejecutar consultas de Hive en el clúster. Si lo desea, también puede cargar su propio archivo de datos en la cuenta de almacenamiento de blobs asociada al clúster. Consulte [Carga de datos en HDInsight][Carga de datos en HDInsight] para obtener instrucciones. Para obtener más información sobre el uso del almacenamiento de blobs de Azure con HDInsight, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][Uso del almacenamiento de blobs de Azure con HDInsight].

En este artículo se usa un archivo de ejemplo *log4j* que se distribuye el clúster de HDInsight y se almacena en *\\example\\data\\sample.log* en el contenedor de almacenamiento de blobs. También puede generar su propios archivos log4j con la utilidad de registro [Apache Log4j][Apache Log4j] y luego cargarlos en el contenedor de blobs. Cada registro del archivo consta de una línea de campos que contiene un campo `[LOG LEVEL]` para mostrar el tipo y la gravedad. Por ejemplo:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937 

En el ejemplo anterior, el nivel de registro es ERROR.

Puede obtener acceso a los archivos usando la siguiente sintaxis desde la aplicación:

    wasb://<containerName>@<AzureStorageName>.blob.core.windows.net

Por lo tanto, para tener acceso al archivo sample.log, use la siguiente sintaxis:

    wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

Reemplace *mycontainer* por el nombre del contenedor de blobs de Azure y *mystorage* por el nombre de la cuenta de almacenamiento de Azure.

Como el archivo se almacena en el sistema de archivos predeterminado, también puede acceder al archivo usando lo siguiente:

    wasb:///example/data/sample.log
    /example/data/sample.log

## <span id="runhivequeries"></span></a> Ejecución de las consultas de Hive usando PowerShell

En la última sección, identificó un archivo *sample.log* que usará para ejecutar las consultas de Hive. En esta, ejecutará HiveQL para crear una tabla de Hive, cargar los datos de ejemplo en ella y después consultar los datos para determinar cuántos registros de error hay en el archivo.

Este artículo proporciona las instrucciones para usar los cmdlets de Azure PowerShell para ejecutar una consulta de Hive. Antes de continuar con esta sección, debe configurar el entorno local y la conexión a Azure como se indica en la sección de **Requisitos previos** al inicio de este tema.

Las consultas de Hive se pueden ejecutar en PowerShell usando el cmdlet **Start-AzureHDInsightJob** o el cmdlet **Invoke-Hive**.

**Para ejecutar las consultas de Hive usando Start-AzureHDInsightJob**

1.  Abra una ventana de la consola de Azure PowerShell. Las instrucciones se encuentran en [Instalación y configuración de Azure PowerShell][Instalación y configuración de Azure PowerShell].
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

4.  Ejecute el script siguiente para definir las consultas de HiveQL. Puede elegir entre crear una tabla de Hive *interna* o *externa*.

    -   Con las tablas internas, los datos de ejemplo que utilice se mueven desde su ubicación existente hasta la carpeta \\hive\\warehouse\\\<*nombre de tabla\>*. Por lo tanto, cuando elimina una tabla interna, también se eliminan los datos asociados. Si utiliza las tablas internas y desea volver a ejecutar el script, debe cargar de nuevo el archivo *sample.log* en el almacenamiento de blobs.
    -   Con las tablas externas, los datos no se mueven de su ubicación original. También puede usar la tabla externa en caso de que el archivo de datos se ubique en otro contenedor o en otra cuenta de almacenamiento.

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

    Si la tabla ya existe, el comando DROP TABLE elimina la tabla y el archivo de datos. El comando LOAD DATA de HiveQL mueve el archivo de datos desde \\example\\data hasta la carpeta \\hive\\warehouse\\\<*nombre de tabla\>*.

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

    ![HDI.HIVE.PowerShell][HDI.HIVE.PowerShell]

    El resultado es:

        [ERROR] 3

    lo que indica que habían tres instancias de registros de ERROR en el archivo *sample.log*.

Si es necesario, también puede importar el resultado de las consultas a Microsoft Excel para un análisis adicional. Para obtener instrucciones, consulte [Conexión de Excel a Hadoop con Power Query][Conexión de Excel a Hadoop con Power Query].

**Para enviar las consultas de Hive usando Invoke-Hive**

1.  Abra una ventana de la consola de Azure PowerShell.
2.  Ejecute el comando siguiente para conectarse a su suscripción de Azure:

        Add-AzureAccount

    Se le pedirá que escriba las credenciales de la cuenta de Azure.

3.  Configure las variables en el script siguiente y ejecútelo:

        # Provide Azure subscription name, Azure Storage account, and container.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide HDInsight cluster name Where you want to run the Hive job
        $clusterName = "<HDInsightClusterName>"

4.  Conéctese al clúster de HDInsight.

        # Connect to the cluster
        Use-AzureHDInsightCluster $clusterName

5.  Ejecute el script siguiente para invocar las consultas de HiveQL:

        # Run the query to create an internal Hive table, load sample data
        $response = Invoke-Hive -Query @"
        >> DROP TABLE log4jLogs;
        >> CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW   
            FORMAT DELIMITED FIELDS TERMINATED BY ' ';
        >> LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/
            sample.log' OVERWRITE INTO TABLE log4jLogs;
        >> SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4; 
        >> "@

6.  Imprima el resultado en la consola.

        # print the output on the console
        Write-Host $response

    El resultado tendrá un aspecto similar al siguiente:

    ![PowerShell Invoke-Hive output][PowerShell Invoke-Hive output]

    Para consultas de HiveQL más extensas, puede usar PowerShell Here-Strings o archivos de script de HiveQL. El siguiente fragmento indica cómo usar el cmdlet *Invoke-Hive* para ejecutar un archivo de script de HiveQL. El archivo de script de HiveQL debe cargarse en WASB.

        Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"

    Para obtener más información acerca de Here-Strings, consulte [Uso de Windows PowerShell Here-Strings][Uso de Windows PowerShell Here-Strings].

Si es necesario, también puede importar el resultado de las consultas a Microsoft Excel para un análisis adicional. Para obtener instrucciones, consulte [Conexión de Excel a Hadoop con Power Query][Conexión de Excel a Hadoop con Power Query].

## <span id="usetez"></span></a>Uso de Tez para un mejor rendimiento

[Apache Tez][Apache Tez] es un marco que permite que aplicaciones con uso intensivo de datos como Hive se ejecuten con mucha más eficacia a escala. En la última versión de HDInsight, Hive ya admite la ejecución en Tez. Esta opción está actualmente desactivada de forma predeterminada y debe habilitarse. En versiones de clúster futuras, estará activada de manera predeterminada. Con objeto de aprovechar Tez, debe establecerse el siguiente valor en una consulta de Hive:

        set hive.execution.engine=tez;
        

Este valor puede enviarse por consulta insertándolo al comienzo de la consulta. También se puede establecer para que se active de manera predeterminada en un clúster estableciendo el valor de configuración en el momento de la creación del clúster. Puede encontrar más detalles en [Aprovisionamiento de clústeres de HDInsight][Aprovisionamiento de clústeres de HDInsight].

Los [documentos de diseño de Hive en Tez][documentos de diseño de Hive en Tez] incluyen varios detalles sobre las opciones de implementación y las configuraciones de ajuste.

## <span id="nextsteps"></span></a>Pasos siguientes

Aunque Hive facilita la realización de las consultas de datos usando un lenguaje de consultas de tipo SQL, otros componentes disponibles con HDInsight proporcionan funciones complementarias, como el movimiento y la transformación de los datos. Para obtener más información, consulte los artículos siguientes:

-   [Uso de Oozie con HDInsight][Uso de Oozie con HDInsight]
-   [Envío de trabajos de Hadoop mediante programación][Envío de trabajos de Hadoop mediante programación]
-   [Uso de Pig con HDInsight][Uso de Pig con HDInsight]
-   [Análisis de la información de retraso de vuelos usando HDInsight][Análisis de la información de retraso de vuelos usando HDInsight]
-   [Azure HDInsight SDK documentation][Azure HDInsight SDK documentation]
-   [Carga de datos en HDInsight][Carga de datos en HDInsight]
-   [Introducción a HDInsight de Azure][Introducción a HDInsight]

  [Apache Hive]: http://hive.apache.org/
  [Introducción a HDInsight]: ../hdinsight-get-started/
  [Aprovisionamiento de clústeres de HDInsight]: ../hdinsight-provision-clusters/
  [Instalación y configuración de Azure PowerShell]: ../install-configure-powershell/
  [Caso de uso de Hive]: #usage
  [Carga de datos en tablas de Hive]: #uploaddata
  [Ejecución de las consultas de Hive usando PowerShell]: #runhivequeries
  [Uso de Tez para un mejor rendimiento]: #usetez
  [Pasos siguientes]: #nextsteps
  [HDI.HIVE.Architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
  [Carga de datos en HDInsight]: ../hdinsight-upload-data/
  [Uso del almacenamiento de blobs de Azure con HDInsight]: ../hdinsight-use-blob-storage
  [Apache Log4j]: http://en.wikipedia.org/wiki/Log4j
  [HDI.HIVE.PowerShell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
  [Conexión de Excel a Hadoop con Power Query]: http://azure.microsoft.com/es-es/documentation/articles/hdinsight-connect-excel-power-query/
  [PowerShell Invoke-Hive output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
  [Uso de Windows PowerShell Here-Strings]: http://technet.microsoft.com/es-es/library/ee692792.aspx
  [Apache Tez]: http://tez.apache.org
  [documentos de diseño de Hive en Tez]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
  [Uso de Oozie con HDInsight]: ../hdinsight-use-oozie/
  [Envío de trabajos de Hadoop mediante programación]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Uso de Pig con HDInsight]: ../hdinsight-use-pig/
  [Análisis de la información de retraso de vuelos usando HDInsight]: ../hdinsight-analyze-flight-delay-data/
  [Azure HDInsight SDK documentation]: http://msdnstage.redmond.corp.microsoft.com/es-es/library/dn479185.aspx
