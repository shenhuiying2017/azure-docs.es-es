<properties 
	pageTitle="Administración de clústeres de Hadoop en HDInsight con Azure PowerShell | Azure" 
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
	ms.date="11/21/2014" 
	ms.author="jgao"/>

# Administración de clústeres de Hadoop en HDInsight con Azure PowerShell

Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. En este artículo, aprenderá a administrar clústeres de Hadoop en HDInsight con una consola local de Azure PowerShell mediante el uso de Windows PowerShell. Para obtener más información acerca de los cmdlets de HDInsight PowerShell, consulte [Documentación de referencia de los cmdlets de HDInsight][hdinsight-powershell-reference].

**Requisitos previos:**

Antes de empezar este artículo, debe tener lo siguiente:

- Una suscripción de Azure. Azure es una plataforma basada en suscripción. Los cmdlets de HDInsight PowerShell realizan las tareas con su suscripción. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra][azure-purchase-options], [Ofertas para miembros][azure-member-offers] o [Prueba gratuita][azure-free-trial].

- Una estación de trabajo con Azure PowerShell. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][Powershell-install-configure].

			
	

## En este artículo

* [Aprovisionamiento de un clúster](#provision)
* [Enumeración y visualización de clústeres](#listshow)
* [Eliminación de un clúster](#delete)
* [Concesión/Revocación del acceso a los servicios de HTTP](#httpservices)
* [Envío de trabajos de MapReduce](#mapreduce)
* [Envío de trabajos de Hive](#hive)
* [Carga de datos en el almacenamiento de blobs](#upload)
* [Descarga de datos de salida de MapReduce del almacenamiento de blobs](#download)


##<a id="provision"></a> Aprovisionamiento de un clúster de HDInsight
HDInsight utiliza contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure y un contenedor de almacenamiento antes de crear un clúster de HDInsight. 

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Para crear una cuenta de almacenamiento de Azure**

Después de importar el archivo publishsettings, puede usar el siguiente comando para crear una cuenta de almacenamiento:

	# Create an Azure storage account
	$storageAccountName = "<StorageAcccountName>"
	$location = "<Microsoft data center>"           # For example, "West US"

	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

> [AZURE.NOTE] Dicha cuenta debe ubicarse en el mismo centro de datos que el clúster de HDInsight. En la actualidad, solo pueden aprovisionarse clústeres de HDInsight en los siguientes centros de datos:

><ul>
<li>Sudeste asiático</li>
<li>Europa del Norte</li>
<li>Europa occidental</li>
<li>Este de EE. UU.</li>
<li>Oeste de EE. UU.</li>
</ul>



Para obtener información acerca de la creación de una cuenta de almacenamiento de Azure a través del portal de administración, consulte [Creación, administración o eliminación de una cuenta de almacenamiento](storage-create-storage-account.md).

Si ya tiene una cuenta de almacenamiento pero no sabe su nombre ni su clave, puede usar los comandos siguientes para recuperar dicha información:

	# List storage accounts for the current subscription
	Get-AzureStorageAccount
	# List the keys for a storage account
	Get-AzureStorageKey <StorageAccountName>

Para obtener información acerca de cómo conseguir la información usando el portal de administración, consulte la sección * Visualización, copia y regeneración de claves de acceso de almacenamiento* de [Creación, administración o eliminación de cuentas de almacenamiento](storage-create-storage-account.md).

**Para crear un contenedor de almacenamiento de Azure**

PowerShell no puede crear un contenedor de blobs durante el proceso de aprovisionamiento de HDInsight. Puede crear uno con el siguiente script:

	$storageAccountName = "<StorageAccountName>"
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$containerName="<ContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
	                                       -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**Para aprovisionar un clúster**

Una vez que tenga preparados la cuenta de almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster.    
		
	$storageAccountName = "<StorageAccountName>"
	$containerName = "<ContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<MicrosoftDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the storage account key
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

	# Create a new HDInsight cluster
	New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes


En la siguiente captura de pantalla se muestra la ejecución del script:

![HDI.PS.Provision][image-hdi-ps-provision]




##<a id="listshow"></a> Enumeración y visualización de los detalles del clúster
Use los comandos siguientes para enumerar y mostrar los detalles del clúster:

**Para enumerar todos los clústeres en la suscripción actual**

	Get-AzureHDInsightCluster 

**Para mostrar los detalles del clúster específico en la suscripción actual**

	Get-AzureHDInsightCluster -Name <ClusterName> 

##<a id="delete"></a> Eliminación de un clúster
Use el comando siguiente para eliminar un clúster:

	Remove-AzureHDInsightCluster -Name <ClusterName> 

##<a id="httpservice"></a> Concesión/Revocación del acceso a los servicios de HTTP

Los clústeres de HDInsight tienen los siguientes servicios web HTTP (todos estos servicios tienen extremos RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


De manera predeterminada, estos servicios se conceden para el acceso. Puede revocar/conceder el acceso.  A continuación se ofrece una muestra:

	Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

En la muestra <i>hdiv2</i> es un nombre de clúster de HDInsight.

>[AZURE.NOTE] Al conceder/revocar el acceso, restablecerá el nombre de usuario y la contraseña del usuario del clúster.

Esto también se puede hacer a través del Portal de administración de Azure. Consulte [Administración de HDInsight con el portal de administración][hdinsight-admin-portal].

##<a id="mapreduce"></a> Envío de trabajos de MapReduce
La distribución de clústeres de HDInsight se incluye con algunas muestras de MapReduce. Una de las muestras ilustra el recuento de la frecuencia de las palabras en los archivos de código fuente.

**Para enviar un trabajo de MapReduce**

El siguiente script de PowerShell envía el trabajo de muestra de recuento de palabras: 
	
	$clusterName = "<HDInsightClusterName>"            
	
	# Define the MapReduce job
	$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
	
	# Run the job and show the standard error 
	$wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}
	
> [AZURE.NOTE] *hadoop-examples.jar* incluye los clústeres de HDInsight versión 2.1. Se cambió el nombre del archivo a  *hadoop-mapreduce.jar* en la versión 3.0 de los clústeres de HDInsight.

Para obtener información acerca del prefijo WASB, consulte [Uso del almacenamiento de blobs de Azure para HDInsight][hdinsight-storage].

**Para descargar la salida del trabajo de MapReduce**

La siguiente script de PowerShell recupera la salida del trabajo de MapReduce desde el último procedimiento:

	$storageAccountName = "<StorageAccountName>"   
	$containerName = "<ContainerName>"             
		
	# Create the storage account context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Download the output to local computer
	Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
	
	# Display the output
	cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

Para obtener más información acerca del desarrollo y la ejecución de trabajos de MapReduce, consulte [Uso de MapReduce con HDInsight][hdinsight-use-mapreduce].


##<a id="hive"></a> Envío de trabajos de Hive
La distribución de clústeres de HDInsight se incluye con una tabla de muestra de Hive llamada  *hivesampletable*. Puede usar una consulta "show tables;" de HiveQL para enumerar las tablas de Hive en un clúster.

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

El trabajo de Hive mostrará primero las tablas de Hive creadas en el clúster y los datos devueltos por hivesampletable.

Para obtener más información acerca del uso de Hive, consulte [Uso de Hive con HDInsight][hdinsight-use-hive].


##<a id="upload"></a>Carga de datos en el almacenamiento de blobs
Consulte [Carga de datos en HDInsight][hdinsight-upload-data].

##<a id="download"></a>Descarga de datos de salida de MapReduce del almacenamiento de blobs
Consulte la sesión [Envío de trabajos de MapReduce](#mapreduce) en este artículo.

## Otras referencias
* [Documentación de referencia de los cmdlets de HDInsight][hdinsight-powershell-reference]
* [Administración de HDInsight con el portal de administración][hdinsight-admin-portal]
* [Administración de HDInsight con la interfaz de línea de comandos][hdinsight-admin-cli]
* [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision]
* [Carga de datos en HDInsight][hdinsight-upload-data]
* [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs]
* [Introducción a HDInsight de Azure][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-provision]: ../hdinsight-provision-clusters/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/

[hdinsight-admin-cli]: ../hdinsight-administer-use-command-line/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-upload-data]: ../hdinsight-upload-data/

[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx

[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png



<!--HONumber=42-->
