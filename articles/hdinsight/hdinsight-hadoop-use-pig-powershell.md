<properties
   pageTitle="Uso de Pig con Hadoop con PowerShell en HDInsight | Microsoft Azure"
   description="Aprenda a enviar trabajos de Pig a un clúster de Hadoop en HDInsight mediante Azure PowerShell."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/23/2015"
   ms.author="larryfr"/>

#Ejecución de trabajos de Pig mediante PowerShell

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Este documento proporciona un ejemplo de uso de Azure PowerShell para enviar trabajos de Pig a un clúster de Hadoop en HDInsight. Pig le permite escribir trabajos de MapReduce mediante un lenguaje (Pig Latin) que modela las transformaciones de datos, en lugar de asignar y reducir las funciones.

> [AZURE.NOTE]Este documento no ofrece una descripción detallada de cómo funcionan las instrucciones de Pig Latin que se usan en los ejemplos. Para obtener información sobre Pig Latin utilizado en este ejemplo, consulte [Uso de Hive con Hadoop en HDInsight](hdinsight-use-pig.md).

##<a id="prereq"></a>Requisitos previos

Necesitará lo siguiente para completar los pasos de este artículo.

- **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Una estación de trabajo con Azure PowerShell**. Vea [Instalar y usar Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).


##<a id="powershell"></a>Ejecución de trabajos de Pig mediante PowerShell

Azure PowerShell proporciona *cmdlets* que le permiten ejecutar de manera remota trabajos de Pig en HDInsight. De manera interna, esto se logra mediante llamadas REST a [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente llamado Templeton), que se ejecuta en el clúster de HDInsight.

Los cmdlets siguientes se utilizan cuando se ejecutan trabajos de Pig en un clúster de HDInsight remoto:

* **Add-AzureAccount**: autentica Azure PowerShell para la suscripción de Azure.

* **New-AzureHDInsightPigJobDefinition**: crea una nueva *definición de trabajo* con las instrucciones de Pig Latin especificadas.

* **Start-AzureHDInsightJob**: envía la definición del trabajo a HDInsight, inicia el trabajo y devuelve un objeto de *trabajo* que se puede utilizar para comprobar el estado del trabajo.

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

2. Abra un nuevo símbolo del sistema de Azure PowerShell. Cambie los directorios a la ubicación del archivo pigjob.ps1 y, a continuación, utilice el siguiente comando para ejecutar el script:

		.\pigjob.ps1

7. Cuando el trabajo se complete, debe devolver información de manera similar a la siguiente:

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

##<a id="troubleshooting"></a>Solución de problemas

Si no se devuelve ninguna información cuando se completa el trabajo, pudo haberse producido un error durante el procesamiento. Para ver la información de error para este trabajo, agregue lo siguiente al final del archivo **pigjob.ps1**, guárdelo y, a continuación, ejecútelo de nuevo.

	# Print the output of the Pig job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardError

Se devuelve la información escrita en STDERR en el servidor cuando ejecutó el trabajo y puede ayudar a determinar por qué se ha producido el error en el trabajo.

##<a id="summary"></a>Resumen

Como puede ver, Azure PowerShell proporciona una manera fácil de ejecutar trabajos de Pig en un clúster de HDInsight, de supervisar el estado del trabajo y de recuperar el resultado.

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre Pig en HDInsight, siga estos pasos:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

Para obtener información sobre otras maneras de trabajar con Hadoop en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=Sept15_HO4-->