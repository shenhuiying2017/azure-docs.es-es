<properties
	pageTitle="Administración de clústeres de Hadoop en HDInsight con Azure PowerShell | Microsoft Azure"
	description="Vea cómo realizar tareas administrativas para clústeres de Hadoop en HDInsight usando Azure PowerShell."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	tags="azure-portal"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="jgao"/>

# Administración de clústeres de Hadoop en HDInsight con PowerShell de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. En este artículo, aprenderá a administrar clústeres de Hadoop en HDInsight de Azure con una consola local de PowerShell de Azure mediante el uso de Windows PowerShell. Para obtener más información acerca de los cmdlets de PowerShell de HDInsight, consulte [Documentación de referencia de los cmdlets de HDInsight][hdinsight-powershell-reference].



**Requisitos previos**

Antes de empezar este artículo, debe tener lo siguiente:

- **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Una estación de trabajo con Azure PowerShell**. Consulte [Install and use Azure PowerShell (Instalación y uso de Azure PowerShell)](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

	> [AZURE.NOTE]Los scripts de PowerShell que se proporcionan en este artículo usan el modo de administrador de recursos de Azure. Para asegurarse de que los ejemplos le funcionen, descargue la última versión de Azure PowerShell con el Instalador de plataforma web de Microsoft.

##Aprovisionamiento de clústeres de HDInsight

Los clústeres de HDInsight requieren un grupo de recursos de Azure y un contenedor de blobs en una cuenta de almacenamiento de Azure:

- Un grupo de recursos de Azure es un contenedor lógico para los recursos de Azure. El grupo de recursos de Azure y el clúster de HDInsight no tienen que estar en la misma ubicación. Para obtener más información, consulte [Uso de Azure PowerShell con el Administrador de recursos de Azure](powershell-azure-resource-manager.md).
- HDInsight usa un contenedor de blobs de una cuenta de almacenamiento de Azure como sistema de archivos predeterminado. Es necesario tener una cuenta de Almacenamiento de Azure y un contenedor de almacenamiento antes de crear un clúster de HDInsight. La cuenta de almacenamiento predeterminada y el clúster de HDInsight deben estar en la misma ubicación.

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Para crear un grupo de recursos de Azure**

1. Asegúrese de que está en el modo de recursos de Azure:

		Switch-AzureMode -Name AzureResourceManager

2. Conéctese a la cuenta de Azure y seleccione una suscripción (si dispone de varias).

		Add-AzureAccount
		Select-AzureSubscription

3. Cree un nuevo grupo de recursos:

	New-AzureResourceGroup -name <AzureResourceGroupName> -Location <AzureDataCente> # Por ejemplo, "West US"

	[AZURE.INCLUDE [data center list](../../includes/hdinsight-pricing-data-centers-clusters.md)]

**Para crear una cuenta de almacenamiento de Azure**

	New-AzureStorageAccount -ResourceGroupName <AzureResourceGroupName> -Name <AzureStorageAccountName> -Location <AzureDataCneter> -Type <AccountType> # account type example: Standard_ZRS for zero redundancy storage

	For a full list of the storage account types, see [https://msdn.microsoft.com/es-es/library/azure/hh264518.aspx](https://msdn.microsoft.com/es-es/library/azure/hh264518.aspx).


Para obtener información sobre la creación de una cuenta de almacenamiento de Azure a través del portal de vista previa de Azure, consulte [Crear, administrar o eliminar una cuenta de almacenamiento](storage-create-storage-account.md).

Si ya tiene una cuenta de Almacenamiento pero no sabe su nombre ni su clave, puede usar los comandos siguientes para recuperar dicha información:

	# List Storage accounts for the current subscription
	Get-AzureStorageAccount
	# List the keys for a Storage account
	Get-AzureStorageAccountKey -ResourceGroupName <AzureResourceGroupName> -name $storageAccountName <AzureStorageAccountName>

Para recibir instrucciones sobre cómo obtener la información mediante el portal de vista previa de Azure, vea la sección "Vista, copia y regeneración de las claves de acceso de almacenamiento" de [Crear, administrar o eliminar una cuenta de almacenamiento](storage-create-storage-account.md).

**Para crear un contenedor de almacenamiento de Azure**

PowerShell de Azure no puede crear un contenedor de blobs durante el proceso de aprovisionamiento de HDInsight. Puede crear uno con el siguiente script:

	$resourceGroupName = "<AzureResoureGroupName>"
	$storageAccountName = "<AzureStorageAccountName>"
	$storageAccountKey = Get-AzureStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }
	$containerName="<AzureBlobContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**Para aprovisionar un clúster**

Cuando tenga preparados la cuenta de almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster.

	$resourceGroupName = "<AzureResoureGroupName>"

	$storageAccountName = "<AzureStorageAccountName>"
	$containerName = "<AzureBlobContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<AzureDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the Storage account key
	$storageAccountKey = Get-AzureStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }

	# Create a new HDInsight cluster
	New-AzureHDInsightCluster -ResourceGroupName $resourceGroupName `
		-ClusterName $clusterName `
		-Location $location `
		-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		-DefaultStorageAccountKey $storageAccountKey `
		-DefaultStorageContainer $containerName  `
		-ClusterSizeInNodes $clusterNodes

##Enumeración de los detalles del clúster
Use el comando siguiente para enumerar todos los clústeres en la suscripción actual:

	Get-AzureHDInsightCluster

Use el comando siguiente para mostrar los detalles de un clúster específico en la suscripción actual:

	Get-AzureHDInsightCluster -ResourceGroupName <ResouceGroupName> -ClusterName <ClusterName>

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

Esto también se puede hacer a través del portal de vista previa. Consulte [Administración de HDInsight mediante el portal de vista previa de Azure][hdinsight-admin-portal].

##Escalado de clústeres
La característica de escalado de clústeres permite cambiar la cantidad de nodos de trabajo que usa un clúster que se ejecuta en HDInsight de Azure sin necesidad de volver a crear el clúster.

>[AZURE.NOTE]Solo son compatibles los clústeres con la versión 3.1.3 de HDInsight, o superior. Si no está seguro de la versión del clúster, puede comprobar la página de propiedades. Consulte [Familiarizarse con la interfaz del portal del clúster](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface).

A continuación se muestra el efecto que tiene cambiar la cantidad de nodos de datos de cada tipo de clúster compatible con HDInsight:

- Hadoop

	Puede aumentar sin ningún problema la cantidad de nodos de trabajo en un clúster de Hadoop que se encuentre en ejecución, sin que afecte a ningún trabajo pendiente o en ejecución. También se pueden enviar trabajos nuevos mientras la operación está en curso. Los errores que puedan surgir en una operación de escalado se enfrentan oportunamente, por lo que el clúster siempre queda en estado funcional.

	Cuando se realiza la reducción vertical de un clúster de Hadoop al disminuir la cantidad de nodos de datos, se reinician algunos de los servicios del clúster. Esto provoca que todos los trabajos pendientes y en ejecución fallen al completarse la operación de escalado. Sin embargo, puede volver a enviar los trabajos una vez finalizada la operación.

- HBase

	Puede agregar nodos sin problemas al clúster de HBase mientras se encuentra en ejecución, así como eliminarlos. Los servidores regionales se equilibran automáticamente en unos pocos minutos tras completar la operación de escalado. Sin embargo, puede equilibrar manualmente los servidores regionales iniciando sesión en el nodo principal del clúster y ejecutando los comandos siguientes desde una ventana del símbolo del sistema:

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

	Para obtener más información sobre el uso del shell HBase, consulte
- Storm

	Puede agregar o quitar sin problemas nodos de datos de su clúster de Storm mientras se encuentra en ejecución. Sin embargo, después de finalizar correctamente la operación de escalado, deberá volver a equilibrar la topología.

	Esto se puede realizar de dos formas:

	* La interfaz de usuario web de Storm
	* La herramienta de la interfaz de línea de comandos (CLI)

	Consulte la [documentación de Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obtener más detalles.

	La interfaz de usuario web de Storm se encuentra disponible en el clúster de HDInsight:

	![reequilibrio de escalado de storm de hdinsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

	El siguiente es un ejemplo de cómo usar el comando CLI para volver a equilibrar la topología de Storm:

		## Reconfigure the topology "mytopology" to use 5 worker processes,
		## the spout "blue-spout" to use 3 executors, and
		## the bolt "yellow-bolt" to use 10 executors

		$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

Para cambiar el tamaño del clúster de Hadoop con Azure PowerShell, ejecute el siguiente comando desde un equipo cliente:

	Set-AzureHDInsightClusterSize -Name <ClusterName> -ClusterSizeInNodes <NewSize>



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
* [Administración de HDInsight mediante el portal de vista previa de Azure][hdinsight-admin-portal]
* [Administración de HDInsight con la interfaz de línea de comandos][hdinsight-admin-cli]
* [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision]
* [Carga de datos en HDInsight][hdinsight-upload-data]
* [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs]
* [Introducción a HDInsight de Azure][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png

<!---HONumber=August15_HO8-->