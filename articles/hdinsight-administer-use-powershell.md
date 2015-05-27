<properties 
	pageTitle="Administración de clústeres de Hadoop en HDInsight con Azure PowerShell | Microsoft Azure" 
	description="Vea cómo realizar tareas administrativas para clústeres de Hadoop en HDInsight usando Azure PowerShell." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="jgao"/>

# Administración de clústeres de Hadoop en HDInsight con PowerShell de Azure


Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. En este artículo, aprenderá a administrar clústeres de Hadoop en HDInsight de Azure con una consola local de PowerShell de Azure mediante el uso de Windows PowerShell. Para obtener más información acerca de los cmdlets de PowerShell de HDInsight, consulte [Documentación de referencia de los cmdlets de HDInsight][hdinsight-powershell-reference].

##Requisitos previos

Antes de empezar este artículo, debe tener lo siguiente:

- Una suscripción de Azure. Azure es una plataforma basada en suscripción. Los cmdlets de PowerShell de Azure para HDInsight realizan las tareas mediante su suscripción. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra][azure-purchase-options], [Ofertas para miembros][azure-member-offers] o [Prueba gratuita][azure-free-trial].

- Una estación de trabajo con Azure PowerShell. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].


##Aprovisionamiento de clústeres de HDInsight
HDInsight utiliza contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es necesario tener una cuenta de Almacenamiento de Azure y un contenedor de almacenamiento antes de crear un clúster de HDInsight.

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Para crear una cuenta de Almacenamiento de Azure**

Después de importar el archivo publishsettings, puede usar el siguiente comando para crear una cuenta de Almacenamiento:

	# Create an Azure Storage account
	$storageAccountName = "<StorageAcccountName>"
	$location = "<Microsoft data center>"           # For example, "West US"

	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location


[AZURE.INCLUDE [data center list](../includes/hdinsight-pricing-data-centers-clusters.md)]


Para obtener información sobre la creación de una cuenta de Almacenamiento de Azure a través del Portal de Azure, consulte [Creación, administración o eliminación de una cuenta de Almacenamiento](../storage-create-storage-account/).

Si ya tiene una cuenta de Almacenamiento pero no sabe su nombre ni su clave, puede usar los comandos siguientes para recuperar dicha información:

	# List Storage accounts for the current subscription
	Get-AzureStorageAccount
	# List the keys for a Storage account
	Get-AzureStorageKey <StorageAccountName>

Para obtener información sobre cómo obtener la información mediante el Portal de Azure, vea la sección "Visualización, copia y regeneración de claves de acceso de almacenamiento" de [Creación, administración o eliminación de una cuenta de Almacenamiento](../storage-create-storage-account/).

**Para crear un contenedor de almacenamiento de Azure**

PowerShell de Azure no puede crear un contenedor de blobs durante el proceso de aprovisionamiento de HDInsight. Puede crear uno con el siguiente script:

	$storageAccountName = "<StorageAccountName>"
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$containerName="<ContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**Para aprovisionar un clúster**

Una vez que tenga preparados la cuenta de Almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster.
		
	$storageAccountName = "<StorageAccountName>"
	$containerName = "<ContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<MicrosoftDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the Storage account key
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

	# Create a new HDInsight cluster
	New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes


En la siguiente captura de pantalla se muestra la ejecución del script:

![HDI.PS.Provision][image-hdi-ps-provision]




##Enumeración de los detalles del clúster
Use el comando siguiente para enumerar todos los clústeres en la suscripción actual:

	Get-AzureHDInsightCluster 

Use el comando siguiente para mostrar los detalles de un clúster específico en la suscripción actual:

	Get-AzureHDInsightCluster -Name <ClusterName> 

##Eliminación de clústeres
Use el comando siguiente para eliminar un clúster:

	Remove-AzureHDInsightCluster -Name <ClusterName> 



##Concesión/Revocación del acceso a los servicios de HTTP

Los clústeres de HDInsight tienen los siguientes servicios web HTTP (todos estos servicios tienen extremos RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


De manera predeterminada, estos servicios se conceden para el acceso. Puede revocar/conceder el acceso. A continuación se ofrece una muestra:

	Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

En el ejemplo, <i>hdiv2</i> es el nombre de un clúster de HDInsight.

>[AZURE.NOTE]Al conceder/revocar el acceso, restablecerá el nombre de usuario y la contraseña del clúster.

Esto también se puede hacer a través del Portal de Azure. Consulte [Administración de HDInsight mediante el Portal de Azure][hdinsight-admin-portal]

##Escalado de clústeres
Consulte [Escalado de clústeres de Hadoop en HDInsight](hdinsight-hadoop-cluster-scaling.md).

##Envío de trabajos de MapReduce
La distribución de clústeres de HDInsight se incluye con algunas muestras de MapReduce. Una de las muestras ilustra el recuento de la frecuencia de las palabras en los archivos de código fuente.

**Para enviar un trabajo de MapReduce**

El siguiente script de PowerShell envía el trabajo de ejemplo de recuento de palabras:
	
	$clusterName = "<HDInsightClusterName>"            
	
	# Define the MapReduce job
	$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
	
	# Run the job and show the standard error 
	$wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}
	
Para obtener información acerca del prefijo **wasb** , consulte [Uso del almacenamiento de blobs de Azure para HDInsight][hdinsight-storage].

**Para descargar la salida del trabajo de MapReduce**

El siguiente script de PowerShell recupera la salida del trabajo de MapReduce desde el último procedimiento:

	$storageAccountName = "<StorageAccountName>"   
	$containerName = "<ContainerName>"             
		
	# Create the Storage account context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Download the output to local computer
	Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
	
	# Display the output
	cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

Para obtener más información acerca del desarrollo y la ejecución de trabajos de MapReduce, consulte [Uso de MapReduce con HDInsight][hdinsight-use-mapreduce].






































##Envío de trabajos de Hive
La distribución de clústeres de HDInsight se incluye con una tabla de ejemplo de Hive llamada *hivesampletable*. Puede usar un comando **SHOW TABLES** de HiveQL para enumerar las tablas de Hive en un clúster.

**Para enviar un trabajo de Hive**

El siguiente script envía un trabajo de Hive para enumerar las tablas de Hive:
	
	$clusterName = "<HDInsightClusterName>"               
	
	# HiveQL query
	$querystring = @"
		SHOW TABLES;
		SELECT * FROM hivesampletable 
			WHERE Country='United Kingdom'
			LIMIT 10;
	"@

	Use-AzureHDInsightCluster -Name $clusterName
	Invoke-Hive $querystring

El trabajo de Hive mostrará primero las tablas de Hive creadas en el clúster y los datos devueltos de la tabla hivesampletable.

Para obtener más información acerca del uso de Hive, consulte [Uso de Hive con HDInsight][hdinsight-use-hive].


##Carga de archivos de datos al almacenamiento de blobs de Azure
Consulte [Carga de datos en HDInsight][hdinsight-upload-data].

##Descarga de la salida del trabajo del almacenamiento de blobs de Azure
Consulte la sección [Envío de trabajos de MapReduce](#mapreduce) en este artículo.

## Otras referencias
* [Documentación de referencia de los cmdlets de HDInsight][hdinsight-powershell-reference]
* [Administración de HDInsight mediante el Portal de Azure][hdinsight-admin-portal]
* [Administración de HDInsight con la interfaz de línea de comandos][hdinsight-admin-cli]
* [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision]
* [Carga de datos en HDInsight][hdinsight-upload-data]
* [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs]
* [Introducción a HDInsight de Azure][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx

[powershell-install-configure]: install-configure-powershell.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png



<!--HONumber=54-->