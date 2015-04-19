<properties
   pageTitle="Uso de MapReduce con Hadoop en HDInsight | Azure"
   description="Obtenga información sobre cómo usar PowerShell para ejecutar trabajos de MapReduce de forma remota con Hadoop en HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Ejecución de consultas de Hive con Hadoop en HDInsight con PowerShell

[AZURE.INCLUDE [mapreduce-selector](../includes/hdinsight-selector-use-mapreduce.md)]

Este documento proporciona un ejemplo de uso de PowerShell para ejecutar un trabajo de MapReduce en un Hadoop de un clúster de HDInsight.

## <a id="prereq"></a>Requisitos previos

Necesitará lo siguiente para completar los pasos de este artículo.

* Un clúster de HDInsight de Azure (Hadoop en HDInsight) (Windows o Linux)

* <a href="http://azure.microsoft.com/documentation/articles/install-configure-powershell/" target="_blank">Azure PowerShell</a>

## <a id="powershell"></a>Ejecución de un trabajo de MapReduce mediante PowerShell

Azure PowerShell ofrece *cmdlets* que permiten ejecutar trabajos de MapReduce de forma remota en HDInsight. Internamente, esto se logra mediante el uso de las llamadas de REST a <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat" target="_blank">WebHCat</a> (anteriormente denominado Templeton), que se ejecutan en el clúster de HDInsight.

Los siguientes cmdlets se utilizan al ejecutar trabajos de MapReduce en un clúster de HDInsight remoto.

* **Add-AzureAccount**: autentica PowerShell para la suscripción de Azure

* **New-AzureHDInsightMapReduceJobDefinition**: crea una nueva *job definition* utilizando la información de MapReduce especificada

* **Start-AzureHDInsightJob**: envía la definición del trabajo en HDInsight, se inicia el trabajo y devuelve un objeto *job* que puede utilizarse para comprobar el estado del trabajo

* **Wait-AzureHDInsightJob**: utiliza el objeto job para comprobar el estado del trabajo. Esperará hasta que el trabajo se haya completado o ha superado el tiempo de espera

* **Get-AzureHDInsightJobOutput**: se usa para recuperar el resultado del trabajo

Los pasos siguientes muestran cómo usar estos cmdlets para ejecutar un trabajo en el clúster de HDInsight.

1. Mediante un editor, guarde el código siguiente como **mapreducejob.ps1**. Debe reemplazar **CLUSTERNAME** por el nombre de su clúster de HDInsight.

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"

		#Define the MapReduce job
		#NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
		# -JarFile = the JAR containing the MapReduce application
		# -ClassName = the class of the application
		# -Arguments = The input file, and the output directory
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
		                          -ClassName "wordcount" `
		                          -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

		#Submit the job to the cluster
		Write-Host "Start the MapReduce job..." -ForegroundColor Green
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition

		#Wait for the job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600

		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardOutput

2. Abra un nuevo símbolo del sistema de **Microsoft Azure PowerShell**. Cambie los directorios a la ubicación del archivo **mapreducejob.ps1** y, a continuación, utilice lo siguiente para ejecutar el script.

		.\mapreducejob.ps1

3. Cuando se completa el trabajo, obtendrá un resultado similar al siguiente.

		Cluster         : CLUSTERNAME
		ExitCode        : 0
		Name            : wordcount
		PercentComplete : map 100% reduce 100%
		Query           :
		State           : Completed
		StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
		SubmissionTime  : 12/5/2014 8:34:09 PM
		JobId           : job_1415949758166_0071

	> [AZURE.NOTE] Si **ExitCode** es un valor distinto de 0, consulte [Solución de problemas](#troubleshooting).

	Esto indica que el trabajo se ha completado correctamente.

## <a id="results"></a>Visualización del resultado del trabajo

El trabajo de MapReduce almacena los resultados de la operación de almacenamiento de blobs de Azure, en la ruta de acceso **wasb:///example/data/WordCountOutput** ruta de acceso especificada como argumento para el trabajo. Almacenamiento de blobs de Azure es accesible a través de Azure PowerShell, pero debe conocer el nombre de la cuenta de almacenamiento, la clave y un contenedor que el clúster de HDInsight utiliza para tener acceso directamente a los archivos.

Afortunadamente, puede obtener esta información mediante los siguientes cmdlets de PowerShell de Azure:

* **Get-AzureHDInsightCluster**: devuelve información acerca de un clúster de HDInsight, incluidas las cuentas de almacenamiento asociadas. Siempre habrá una cuenta de almacenamiento predeterminada asociada a un clúster.
* **Nueva AzureStorageContext**: dado el nombre de la cuenta de almacenamiento y clave recuperada mediante **Get AzureHDInsightCluster**, devuelve un objeto de contexto que puede utilizarse para tener acceso a la cuenta de almacenamiento.
* **Get-AzureStorageBlob**: dado un objeto y el contenedor de nombre de contexto, devuelve una lista de blobs dentro del contenedor.
* **Get-AzureStorageBlobContent**: dado un objeto de contexto, una ruta de acceso y nombre y un nombre de contenedor (devuelto por **Get AzureHDinsightCluster**), descarga un archivo desde el almacenamiento de blobs de Azure.

En el siguiente ejemplo se recupera la información de almacenamiento y después se descarga el resultado de **wasb:///example/data/WordCountOutput**. Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight.

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"

		#Retrieve the cluster information
		$clusterInfo = Get-AzureHDInsightCluster -ClusterName $clusterName

		#Get the storage account information
		$storageAccountName = $clusterInfo.DefaultStorageAccount.StorageAccountName
		$storageAccountKey = $clusterInfo.DefaultStorageAccount.StorageAccountKey
		$storageContainer = $clusterInfo.DefaultStorageAccount.StorageContainerName

		#Create the context object
		$context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		#Download the files from wasb:///example/data/WordCountOutput
		#Use the -blob switch to filter only blobs contained in example/data/WordCountOutput
		Get-AzureStorageBlob -Container $storageContainer -Blob example/data/WordCountOutput/* -Context $context | Get-AzureStorageBlobContent -Context $context

> [AZURE.NOTE] En este ejemplo se almacenarán los archivos descargados en la  carpeta **ejemplo/data/WordCountOutput** del directorio desde el que se ejecuta el script.

La salida del trabajo de MapReduce se almacena en archivos con el nombre *part-r-#####*. Abra el archivo **example/data/WordCountOutput/part-r-00000** en un editor de texto para ver las palabras y los números generados por el trabajo.

> [AZURE.NOTE] Los archivos de salida de un trabajo de MapReduce no se pueden mover. Por lo tanto, si vuelve a ejecutar esta muestra tendrá que cambiar el nombre del archivo de salida.

## <a id="troubleshooting"></a>Solución de problemas

Si no se devuelve información cuando se completa el trabajo, pudo haberse producido un error durante el procesamiento. Para ver información de error para este trabajo, agregue lo siguiente al final del archivo **mapreducejob.ps1**, guárdelo y después ejecútelo de nuevo.

	# Print the output of the WordCount job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

Se devolverá la información escrita en STDERR en el servidor cuando se ejecuta el trabajo y puede ayudar a determinar por qué se ha producido el error en el trabajo.

## <a id="summary"></a>Resumen

Como puede ver, Azure PowerShell proporciona una manera fácil de ejecutar trabajos de MapReduce en un clúster de HDInsight, de supervisar el estado del trabajo y de recuperar el resultado.

## <a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre los trabajos de MapReduce en HDInsight.

* [Uso de MapReduce en Hadoop de HDInsight](hdinsight-use-mapreduce.md)

Para obtener información sobre otras maneras en que puede trabajar con Hadoop en HDInsight.

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)
<!--HONumber=45--> 
