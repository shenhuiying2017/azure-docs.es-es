<properties
   pageTitle="Uso de Pig con Hadoop en HDInsight | Azure"
   description="Aprenda a enviar trabajos de Pig a Hadoop en HDInsight con PowerShell."
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

# Ejecución de trabajos de Pig mediante PowerShell

[AZURE.INCLUDE [pig-selector](../includes/hdinsight-selector-use-pig.md)]

Este documento proporciona un ejemplo de uso de PowerShell para enviar trabajos de Pig a un clúster de Hadoop en HDInsight. Pig le permite escribir trabajos de MapReduce mediante un lenguaje (Pig Latin) que modela las transformaciones de datos, en lugar de asignar y reducir las funciones.

> [AZURE.NOTE] Este documento no proporciona una descripción detallada de lo que hacen las instrucciones de Pig Latin usadas en los ejemplos. Para obtener información sobre el Pig Latin utilizado en este ejemplo, consulte <a href="../hdinsight-use-pig/" target="_blank">Uso de Pig con Hadoop en HDInsight</a>.

## <a id="prereq"></a>Requisitos previos

Necesitará lo siguiente para completar los pasos de este artículo.

* Un clúster de HDInsight de Azure (Hadoop en HDInsight) (Windows o Linux)

* <a href="http://azure.microsoft.com/ documentation/articles/install-configure-powershell/" target="_blank">Azure PowerShell</a>


## <a id="powershell"></a>Ejecución de trabajos de Pig mediante PowerShell

Azure PowerShell ofrece *cmdlets* que permiten ejecutar de manera remota trabajos de Pig en HDInsight. Internamente, esto se logra mediante el uso de las llamadas de REST a <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat" target="_blank">WebHCat</a> (anteriormente denominado Templeton), que se ejecutan en el clúster de HDInsight.

Los cmdlets siguientes se utilizan cuando se ejecutan trabajos de Pig en un clúster de HDInsight remoto.

* **Add-AzureAccount**: autentica PowerShell para la suscripción de Azure

* **New-AzureHDInsightPigJobDefinition**: crea una nueva *job definition* con las instrucciones de Pig Latin especificadas

* **Start-AzureHDInsightJob**: envía la definición del trabajo en HDInsight, inicia el trabajo y devuelve un objeto *job* que puede utilizarse para comprobar el estado del trabajo

* **Wait-AzureHDInsightJob**: utiliza el objeto de trabajo para comprobar el estado del trabajo. Esperará hasta que el trabajo se haya completado o haya superado el tiempo de espera

* **Get-AzureHDInsightJobOutput**: se usa para recuperar la salida del trabajo.

Los pasos siguientes muestran cómo usar estos cmdlets para ejecutar un trabajo en el clúster de HDInsight. 

1. Mediante un editor, guarde el código siguiente como **pigjob.ps1**. Debe reemplazar **CLUSTERNAME** por el nombre de su clúster de HDInsight.

		#Login to your Azure subscription
		Add-AzureAccount

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"
		#Where the output will be saved
		$statusFolder = "/tutorial/pig/status"

		#Store the Pig Latin into $QueryString
		$QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		"LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		"FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		"GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		"FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		"RESULT = order FREQUENCIES by COUNT desc;" +
		"DUMP RESULT;"

		#Create a new HDInsight Pig Job definition
		$pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString -StatusFolder $statusFolder

		# Start the Pig job on the HDInsight cluster
		Write-Host "Start the Pig job ..." -ForegroundColor Green
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition

		# Wait for the Pig job to complete
		Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

		# Print the output of the Pig job.
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

2. Abra un nuevo símbolo del sistema de **Microsoft Azure PowerShell**. Cambie los directorios a la ubicación del archivo **pigjob.ps1** y, a continuación, utilice lo siguiente para ejecutar el script.

		.\pigjob.ps1

7. Una vez completado el trabajo, debe devolver información de manera similar a la siguiente.

		Start the Pig job ...
		Wait for the Pig job to complete ...

		Cluster         : CLUSTERNAME
		ExitCode        : 0
		Name            :
		PercentComplete : 100% complete
		Query           : LOGS = LOAD 'wasb:///example/data/sample.log';LEVELS = foreach LOGS generate REGEX_EXTRACT($0,
			'(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is
			not null;GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;FREQUENCIES = foreach GROUPEDLEVELS
			generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;RESULT = order FREQUENCIES by
			COUNT desc;DUMP RESULT;
			State           : Completed
			StatusDirectory : /tutorial/pig/status
			SubmissionTime  : 11/20/2014 4:04:58 PM
			JobId           : job_1415949758166_0023

			Display the standard output ...
			(TRACE,816)
			(DEBUG,434)
			(INFO,96)
			(WARN,11)
			(ERROR,6)
			(FATAL,2)

## <a id="troubleshooting"></a>Solución de problemas

Si no se devuelve ninguna información cuando se completa el trabajo, pudo haberse producido un error durante el procesamiento. Para ver la información de error para este trabajo, agregue lo siguiente al final del archivo **pigjob.ps1**, guárdelo y, a continuación, ejecútelo de nuevo.

	# Print the output of the Pig job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardError

Se devolverá la información escrita en STDERR en el servidor cuando se ejecuta el trabajo y puede ayudar a determinar por qué se ha producido el error en el trabajo.

## <a id="summary"></a>Resumen

Como puede ver, Azure PowerShell proporciona una manera fácil de ejecutar trabajos de Pig en un clúster de HDInsight, de supervisar el estado del trabajo y de recuperar el resultado.

## <a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre Pig en HDInsight.

* [Uso de Pig con Hadoop en HDInsight](../hdinsight-use-pig/)

Para obtener información sobre otras maneras en que puede trabajar con Hadoop en HDInsight.

* [Uso de Hive con Hadoop en HDInsight](../hdinsight-use-hive/)

* [Uso de MapReduce con Hadoop en HDInsight](../hdinsight-use-mapreduce/)
<!--HONumber=45--> 
