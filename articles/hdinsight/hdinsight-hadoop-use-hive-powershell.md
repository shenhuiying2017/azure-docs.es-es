<properties
   pageTitle="Uso de Hive de Hadoop con PowerShell en HDInsight | Microsoft Azure"
   description="Use PowerShell para ejecutar consultas de Hive en Hadoop en HDInsight."
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
   ms.date="09/03/2015"
   ms.author="larryfr"/>

#Ejecución de consultas de Hive con PowerShell

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

En este documento se ofrece un ejemplo de uso de Azure PowerShell en el modo Grupo de recursos de Azure para ejecutar consultas de Hive en un clúster Hadoop en HDInsight. Para usar Azure PowerShell en el modo Servicio de Azure, vea [Ejecución de consultas de Hive con el modo ASM de PowerShell](hdinsight-hadoop-use-hive-powershell-v1.md).

> [AZURE.NOTE]Este documento no ofrece una descripción detallada de las instrucciones de HiveQL que se usan en los ejemplos. Para obtener información sobre el lenguaje HiveQL que se usa en este ejemplo, vea [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md).


**Requisitos previos**

Necesitará lo siguiente para completar los pasos de este artículo.

- **Un clúster de HDInsight de Azure (Hadoop en HDInsight) (basado en Windows o en Linux)**
- **Una estación de trabajo con Azure PowerShell**. Consulte [Install and use Azure PowerShell (Instalación y uso de Azure PowerShell)](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

##Ejecución de consultas de Hive con Azure PowerShell

Azure PowerShell proporciona *cmdlets* que le permiten ejecutar de manera remota las consultas de Hive en HDInsight. De manera interna, esto se logra mediante llamadas REST a [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente llamado Templeton), que se ejecuta en el clúster de HDInsight.

Los siguientes cmdlets se utilizan al ejecutar las consultas de Hive en un clúster de HDInsight remoto:

* **Add-AzureAccount**: autentica Azure PowerShell para la suscripción de Azure.

* **New-AzureHDInsightHiveJobDefinition**: crea una nueva *definición de trabajo* con las instrucciones especificadas HiveQL.

* **Start-AzureHDInsightJob**: envía la definición del trabajo a HDInsight, inicia el trabajo y devuelve un objeto de *job* que se puede utilizar para comprobar el estado del trabajo.

* **Wait-AzureHDInsightJob**: utiliza el objeto de trabajo para comprobar el estado del trabajo. Esperará hasta que el trabajo se complete o se supere el tiempo de espera.

* **Get-AzureHDInsightJobOutput**: se usa para recuperar la salida del trabajo.

* **Invoke-AzureHDInsightHiveJob**: se usa para ejecutar instrucciones de HiveQL. Esto bloqueará la consulta completa, a continuación, devuelve los resultados.

* **Use-AzureHDInsightCluster**: establece el clúster actual para utilizarlo para el comando **Invoke-Hive**.

Los pasos siguientes muestran cómo usar estos cmdlets para ejecutar un trabajo en el clúster de HDInsight:

1. Mediante un editor, guarde el código siguiente como **hivejob.ps1**. Debe reemplazar **CLUSTERNAME** por el nombre de su clúster de HDInsight.

		#Specify the values
		$clusterName = "CLUSTERNAME"
		$resourceGroupName = "RESOURCEGROUPNAME"
		$httpUsername = "HTTPUSERNAME"
		$httpUserPassword  = "HTTPUSERPASSWORD"

		# Switch to the ARM mode
		Switch-AzureMode -Name AzureResourceManager
		
		# Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#HiveQL
		$queryString = "DROP TABLE log4jLogs;" +
				       "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
				       "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

		#Create an HDInsight Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString 

		#Submit the job to the cluster
		Write-Host "Start the Hive job..." -ForegroundColor Green

		$passwd = ConvertTo-SecureString $httpUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($httpUsername, $passwd)
		$hiveJob = Start-AzureHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds


		#Wait for the Hive job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -StandardOutput 

2. Abra un nuevo símbolo del sistema de **Azure PowerShell**. Cambie los directorios a la ubicación del archivo **hivejob.ps1** y, a continuación, utilice el siguiente comando para ejecutar el script:

		.\hivejob.ps1

7. Cuando el trabajo se complete, debe devolver información de manera similar a la siguiente:

		Display the standard output...
		[ERROR]	3

4. Como se mencionó anteriormente **Invoke-Hive** puede utilizarse para ejecutar una consulta y esperar la respuesta. Utilice los comandos siguientes y reemplace **CLUSTERNAME** por el nombre del clúster:

		Use-AzureHDInsightCluster CLUSTERNAME
		Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		SELECT * FROM errorLogs;
		"@

	El resultado tendrá un aspecto similar al siguiente:

		2012-02-03	18:35:34	SampleClass0	[ERROR]	incorrect	id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	incorrect	id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	incorrect	id

	> [AZURE.NOTE]Para consultas de HiveQL más extensas, puede usar el cmdlet **Here-Strings** de Azure PowerShell o archivos de script de HiveQL. El siguiente fragmento de código indica cómo usar el cmdlet **Invoke-Hive** para ejecutar un archivo de script de HiveQL. El archivo de script de HiveQL debe cargarse en wasb://.
	>
	> `Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> Para obtener más información acerca de **Here-Strings**, vea <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Uso de Here-Strings de Windows PowerShell</a>.

##Solución de problemas

Si no se devuelve ninguna información cuando se completa el trabajo, pudo haberse producido un error durante el procesamiento. Para ver información de error para este trabajo, agregue lo siguiente al final del archivo **hivejob.ps1**, guárdelo y, a continuación, ejecútelo de nuevo.

	# Print the output of the Hive job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardError

Se devuelve la información escrita en STDERR en el servidor cuando ejecute el trabajo y puede ayudar a determinar por qué se ha producido el error en el trabajo.

##Resumen

Como puede ver, Azure PowerShell proporciona una manera fácil de ejecutar consultas de Hive en un clúster de HDInsight, de supervisar el estado del trabajo y de recuperar la salida.

##Pasos siguientes

Para obtener información general acerca de Hive en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras maneras en que puede trabajar con Hadoop en HDInsight:

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=Oct15_HO3-->