<properties
   pageTitle="Uso de Hive de Hadoop en HDInsight | Azure"
   description="Uso de Hive de Hadoop en HDInsight a través de PowerShell."
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

#Ejecución de las consultas de Hive usando PowerShell

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

Este documento proporciona un ejemplo de uso de PowerShell para ejecutar consultas de Hive en un Hadoop en clúster de HDInsight.

> [AZURE.NOTE] Este documento no proporciona una descripción detallada de lo que hacen las instrucciones HiveQL que se usan en los ejemplos. Para obtener información sobre HiveQL que se utiliza en este ejemplo, consulte <a href="../hdinsight-use-hive/" target="_blank">Uso de Hive con Hadoop en HDInsight</a>.


##<a id="prereq"></a>Requisitos previos

Necesitará lo siguiente para completar los pasos de este artículo.

* Un clúster de HDInsight de Azure (Hadoop en HDInsight) (Windows o Linux)

* <a href="http://azure.microsoft.com/documentation/articles/install-configure-powershell/" target="_blank">Azure PowerShell</a>


##<a id="powershell"></a>Ejecución de consultas de Hive con PowerShell

Azure PowerShell proporciona *cmdlets* que le permiten ejecutar de manera remota las consultas de Hive en HDInsight. De manera interna, esto se logra mediante llamadas REST a <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat" target="_blank">WebHCat</a> (anteriormente llamado Templeton), que se ejecuta en el clúster de HDInsight.

Los siguientes cmdlets se utilizan al ejecutar las consultas de Hive en un clúster de HDInsight remoto.

* **Add-AzureAccount**: autentica PowerShell para la suscripción de Azure

* **New-AzureHDInsightHiveJobDefinition**: crea una nueva *definición de trabajo* con las instrucciones especificadas HiveQL.

* **Start-AzureHDInsightJob**: envía la definición del trabajo a HDInsight, inicia el trabajo y devuelve un objeto de *trabajo* que se puede utilizar para comprobar el estado del trabajo.

* **Wait-AzureHDInsightJob**: utiliza el objeto de trabajo para comprobar el estado del trabajo. Esperará hasta que el trabajo se haya completado o haya superado el tiempo de espera

* **Get-AzureHDInsightJobOutput**: se usa para recuperar el resultado del trabajo

* **Invoke-Hive**: se usa para ejecutar instrucciones de HiveQL y bloquearlas cuando se complete. A continuación, devuelve los resultados

* **Use-AzureHDInsightCluster**: establece el clúster actual para utilizarlo para el comando **Invoke-Hive**

Los pasos siguientes muestran cómo usar estos cmdlets para ejecutar un trabajo en el clúster de HDInsight. 

1. Mediante un editor, guarde el código siguiente como **hivejob.ps1**. Debe reemplazar **CLUSTERNAME** por el nombre de su clúster de HDInsight.

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}
		
		#Specify the cluster name
		$clusterName = "CLUSTERNAME" 
		
		#HiveQL
		$queryString = "DROP TABLE log4jLogs;" +
				       "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
				       "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"
		
		#Create an HDInsight Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString
		
		#Submit the job to the cluster
		Write-Host "Start the Hive job..." -ForegroundColor Green
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition
		
		#Wait for the Hive job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600
		
		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

2. Abra un nuevo símbolo del sistema de **Microsoft Azure PowerShell**. Cambie los directorios a la ubicación del archivo **hivejob.ps1** y, a continuación, utilice lo siguiente para ejecutar el script.

		.\hivejob.ps1

7. Una vez completado el trabajo, debe devolver información similar a la siguiente.

		Display the standard output...
		[ERROR]	3

4. Como se mencionó anteriormente **Invoke-Hive** puede utilizarse para ejecutar una consulta y esperar la respuesta. Utilice los comandos siguientes, reemplazando **CLUSTERNAME** por el nombre del clúster.

		Use-AzureHDInsightCluster CLUSTERNAME
		Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		SELECT * FROM errorLogs;
		"@

	El resultado tendrá un aspecto similar al siguiente.

		2012-02-03	18:35:34	SampleClass0	[ERROR]	incorrect	id	
		2012-02-03	18:55:54	SampleClass1	[ERROR]	incorrect	id	
		2012-02-03	19:25:27	SampleClass4	[ERROR]	incorrect	id

	> [AZURE.NOTE] Para consultas de HiveQL más extensas, puede usar PowerShell Here-Strings o archivos de script de HiveQL. El siguiente fragmento de código muestra cómo usar el cmdlet  *Invoke-Hive* para ejecutar un archivo de script de HiveQL. El archivo de script de HiveQL debe cargarse en WASB.
	>
	> `Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> Para obtener más información acerca de Here-Strings, consulte <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Uso de Windows PowerShell Here-Strings</a>.

##<a id="troubleshooting"></a>Solución de problemas

Si no se devuelve ninguna información cuando se completa el trabajo, pudo haberse producido un error durante el procesamiento. Para ver información de error para este trabajo, agregue lo siguiente al final del archivo **hivejob.ps1**, guárdelo y, a continuación, ejecútelo de nuevo.

	# Print the output of the Hive job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardError

Se devolverá la información escrita en STDERR en el servidor cuando se ejecuta el trabajo y puede ayudar a determinar por qué se ha producido el error en el trabajo.

##<a id="summary"></a>Resumen

Como puede ver, Azure PowerShell proporciona una manera fácil de ejecutar consultas de Hive en un clúster de HDInsight, de supervisar el estado del trabajo y de recuperar el resultado.

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general acerca de Hive en HDInsight.

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras formas en que puede trabajar con Hadoop en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

<!--HONumber=47-->
