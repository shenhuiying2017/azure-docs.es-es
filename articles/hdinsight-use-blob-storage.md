<properties urlDisplayName="Blob Storage with  Hadoop in HDInsight" pageTitle="Consultar Big Data desde almacenamiento de blobs compatible con Hadoop | Azure" metaKeywords="" description="HDInsight usa el almacenamiento de blobs compatible con Hadoop como el almacén de big data para HDFS. Obtenga información acerca de cómo realizar consultas desde el almacenamiento de blobs y almacene los resultados de su análisis." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Query big data from Hadoop-compatible Blob storage for analysis in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="jgao" />


#Consultar Big Data desde almacenamiento de blobs compatible con Hadoop para análisis en HDInsight

Almacenamiento de blobs de bajo coste es una solución de almacenamiento de Azure sólida, de uso general y compatible con Hadoop que se integra sin problemas con HDInsight. A través de una interfaz de sistema de archivos distribuidos (HDFS) de Hadoop, el conjunto completo de componentes de HDInsight puede operar directamente en datos en almacenamiento de blobs. En este tutorial, aprenderá a configurar un contenedor para almacenamiento de blobs y, a continuación, hará frente a los datos dentro.

Almacenar los datos en un almacenamiento de blobs hace que los clústeres de HDInsight que se usan para los cálculos se eliminen de forma segura sin perder los datos del usuario. 

> [WACOM.NOTE]	La sintaxis *asv://* no es compatible con la versión 3.0 de los clústeres de HDInsight y tampoco será compatible con las versiones futuras. Es decir, que los trabajos enviados a un clúster de HDInsight cuya versión sea 3.0 y que use explícitamente la sintaxis "asv://" tendrá errores. Debería usarse la sintaxis *wasb://* en lugar de la anterior. Además, los trabajos enviados a cualquier clúster de HDInsight cuya versión sea 3.0 y que se hayan creado con un metastore existente que contenga referencias explícitas a recursos con la sintaxis asv:// tendrán errores. Estas tiendas de metadatos tendrán que volver a crearse usando wasb:// para recursos de dirección.

> Actualmente HDInsight solo es compatible con blobs en bloques.

> La mayoría de comandos HDFS como <b>ls</b>, <b>copyFromLocal</b>, <b>mkdir</b>, etc., siguen funcionando como se espera. Solamente los comandos específicos de la implementación HDFS nativa (a los que se hace referencia como DFS) como <b>fschk</b> y <b>dfsadmin</b> mostrarán un comportamiento diferente en el almacenamiento de blobs de Azure.

Para obtener información acerca de cómo aprovisionar un clúster de HDInsight, consulte [Introducción a HDInsight][hdinsight-get-started] o [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision].

##En este artículo

* [Arquitectura de almacenamiento de HDInsight](#architecture)
* [Ventajas del almacenamiento de blobs de Azure](#benefits)
* [Preparación de un contenedor para el almacenamiento de blobs](#preparingblobstorage)
* [Archivos de dirección en almacenamiento de blobs](#addressing)
* [Acceso a un blob usando PowerShell](#powershell)
* [Pasos siguientes](#nextsteps)

##<a id="architecture"></a>Arquitectura de almacenamiento de HDInsight
El diagrama siguiente proporciona una panorámica de la arquitectura de almacenamiento de HDInsight:

![Hadoop clusters in HDInsight access and store big data in cost-effective, scalable Hadoop-compatible Azure Blob storage in the cloud.](./media/hdinsight-use-blob-storage/HDI.ASVArch.png "HDInsight Storage Architecture")
  
HDInsight brinda acceso al sistema de archivos distribuidos que se adjunta localmente a los nodos de ejecución. Se puede acceder a este sistema de archivos usando el URI completo. Por ejemplo: 

	hdfs://<namenodehost>/<path>

Además, HDInsight ofrece la capacidad de acceder a los datos almacenados en el almacenamiento de blobs. La sintaxis para acceder al almacenamiento de blobs es la siguiente:

	wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>


Hadoop admite una noción del sistema de archivos predeterminado. El sistema de archivos predeterminado implica un esquema y una autoridad predeterminados y también se puede usar para solucionar rutas relativas. Durante el proceso de aprovisionamiento de HDInsight, se designan una cuenta de almacenamiento de Azure y un contenedor de almacenamiento de blobs específico de dicha cuenta como sistema de archivos predeterminado.

Además de esta cuenta de almacenamiento, puede agregar más cuentas de almacenamiento desde la misma suscripción de Azure o desde otras diferentes durante el proceso de aprovisionamiento. Para obtener instrucciones acerca de cómo agregar más cuentas de almacenamiento, consulte [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision]. 

- **Los contenedores de las cuentas de almacenamiento que están conectados a un clúster:** Como el nombre y la clave de la cuenta se almacenan en *core-site.xml*, tendrá acceso total a los blobs de dichos contenedores.
- **Los contenedores o los blobs públicos de las cuentas de almacenamiento que NO están conectados a un clúster:** Solo tendrá permiso de lectura en los blobs de los contenedores.

	> [WACOM.NOTE]
        > Un contenedor público le permite obtener una lista de todos los blobs disponibles del contenedor en cuestión y obtener sus metadatos. Los blobs públicos le permiten obtener acceso a los blobs solo si conoce la URL exacta. Para obtener más información, consulte <a href="http://msdn.microsoft.com/en-us/library/windowsazure/dd179354.aspx">Restricción del acceso a contenedores y blobs</a>.

- **Los contenedores privados de las cuentas de almacenamiento que NO están conectados a un clúster:** No puede acceder a los blobs que los contenedores a menos que defina la cuenta de almacenamiento cuando envíe los trabajos de WebHCat. Se explica posteriormente en el artículo.


Las cuentas de almacenamiento definidas en el proceso de aprovisionamiento y sus claves se almacenan en %HADOOP_HOME%/conf/core-site.xml.  El comportamiento predeterminado de HDInsight es usar las cuentas de almacenamiento definidas en el archivo core-site.xml. No es recomendable editar el archivo core-site.xml porque se puede volver a crear una imagen del nodo principal (maestro) del clúster o dicho nodo se puede migrar en cualquier momento, por lo que cualquier cambio en los archivos se perderá.

Varios trabajos de WebHCat, lo que incluye Hive, MapReduce, streaming de Hadoop y Pig, pueden llevar una descripción de cuentas de almacenamiento y metadatos con ellos (actualmente funciona para Pig con cuentas de almacenamiento pero no para metadatos). En la sección [Acceso a un blob usando PowerShell](#powershell) de este artículo, hay un ejemplo de esta característica. Para obtener más información, consulte [Uso de un clúster de HDInsight con cuentas de almacenamiento y tiendas de metadatos alternativas](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Los contenedores del almacenamiento de blobs almacenan los datos como pares de clave/valor y no hay jerarquía de directorios. No obstante, el carácter "/" se puede usar en el nombre de la clave para que parezca que el archivo está almacenado dentro de una estructura de directorios. Por ejemplo, la clave de un blob puede ser *input/log1.txt*. No hay directorios *input*, pero dada la presencia del carácter "/" en el nombre de la clave, parece la ruta de un archivo.










##<a id="benefits"></a>Ventajas del almacenamiento de blobs de Azure
El coste de rendimiento implícito por no tener ubicados juntos la ejecución y el almacenamiento se ve mitigado por el modo en que los clústeres de cálculo se aprovisionan cerca de los recursos de la cuenta de almacenamiento, dentro del centro de datos de Azure, donde la red de alta velocidad consigue que los nodos de ejecución sean muy eficientes en el acceso a los datos del almacenamiento de blobs.

Hay varias ventajas asociadas al almacenamiento de datos en blobs en lugar de utilizar HDFS:

* **Reutilización y uso compartido de los datos:** Los datos de HDFS se ubican dentro del clúster de cálculo. Solamente las aplicaciones que tengan acceso al clúster de cálculo podrán usar los datos usando la API HDFS. Se puede acceder a los datos del almacenamiento de blobs a través de las API HDFS o las [API REST de almacenamiento de blobs][blob-storage-restAPI]. Por lo tanto, se puede usar un conjunto mayor de aplicaciones (incluyendo otros clústeres de HDInsight) y herramientas para producir y consumir los datos.
* **Archivado de datos:** Almacenar los datos en un almacenamiento de blobs hace que los clústeres de HDInsight que se usan para los cálculos se eliminen de forma segura sin perder los datos del usuario. 
* **Coste del almacenamiento de datos:** Almacenar datos en DFS es más caro a largo plazo que almacenarlos en el almacenamiento de blobs, ya que el coste de un clúster de cálculo es superior al de un contenedor de almacenamiento de blobs. Además, como no hay que volver a cargar los datos para cada generación de clúster de cálculo, también se ahorra en costes de carga de datos.
* **Escalado horizontal elástico:** Aunque HDFS proporciona un sistema de archivos escalable en horizontal, la escala se determina en función del número de nodos que aprovisione para su clúster. Cambiar la escala puede ser un proceso más complicado que basarse en las capacidades de escalado elástico del almacenamiento de blobs que tiene automáticamente.
* **Replicación geográfica:** Sus contenedores de almacenamiento de blobs se pueden replicar geográficamente mediante el Portal de Azure. Mientras que esto le aporta recuperación geográfica y redundancia de datos, una conmutación por error en la ubicación replicada geográficamente afectaría gravemente a su rendimiento y podría incurrir en costes adicionales. Por lo tanto, nuestra recomendación es que elija la replicación geográfica de forma inteligente y únicamente si merece la pena pagar el coste adicional por el valor de los datos.

Determinados trabajos y paquetes de MapReduce podrían crear resultados intermedios que realmente no desea almacenar en el contenedor de almacenamiento de blobs. En tal caso, puede seguir optando por almacenar los datos en el HDFS local. De hecho, HDInsight usa DFS para varios de estos resultados intermedios en los trabajos de Hive y otros procesos. 





##<a id="preparingblobstorage"></a>Preparación de un contenedor para el almacenamiento de blobs
Para usar los blobs, primero debe crear una [cuenta de almacenamiento de Azure][azure-storage-create].. Como parte de este proceso, debe especificar un centro de datos de Azure que almacenará los objetos que cree con esta cuenta. Tanto el clúster como la cuenta de almacenamiento deben hospedarse en el mismo centro de datos (además, la base de datos SQL de Hive metastore y la base de datos SQL de Oozie metastore deben estar ubicadas en el mismo centro de datos). Cualquiera que sea su ubicación, todos los blobs que cree pertenecerán a algún contenedor de su cuenta de almacenamiento. Este contenedor puede ser un contenedor de almacenamiento de blobs existente creado fuera de HDInsight, o bien un contenedor que se haya creado para un clúster de HDInsight. 



###Creación de un contendor de blobs para HDInsight usando el Portal de administración

Al aprovisionar un clúster de HDInsight desde el Portal de administración de Azure, tiene dos opciones: *creación rápida* y *Creación personalizada*. La opción de creación rápida (Creación rápida) requiere la creación previa de la cuenta de almacenamiento de Azure.  Para obtener instrucciones, consulte [Creación de una cuenta de almacenamiento][azure-storage-create]. 

Al usar la opción de creación rápida, puede elegir una cuenta de almacenamiento existente. El proceso de aprovisionamiento crea un nuevo contenedor con el mismo nombre que el del clúster de HDInsight. Si ya existe un contenedor con el mismo nombre, se usará <nombreClúster>-<x>. Por ejemplo, miClústerHDI-1. Este contenedor se usa como sistema de archivos predeterminado.

![Using Quick Create for a new Hadoop cluster in HDInsight in the Azure portal.][img-hdi-quick-create]
 
Mediante la creación personalizada, dispone de una de las siguientes opciones para la cuenta de almacenamiento predeterminada:

- Usar almacenamiento existente
- Crear nuevo almacenamiento
- Usar almacenamiento de otra suscripción.

Tiene la opción de crear su propio contenedor de blobs o usar un existente.
 
![Option to use an existing storage account for your HDInsight cluster.][img-hdi-custom-create-storage-account]
  




### Creación de un contenedor usando Azure PowerShell.
[Azure PowerShell][powershell-install] se puede usar para crear contenedores de blobs. A continuación se muestra un ejemplo de script de PowerShell:

	$subscriptionName = "<SubscriptionName>"	# Azure subscription name
	$storageAccountName = "<AzureStorageAccountName>" # The storage account that you will create
	$containerName="<BlobContainerToBeCreated>" # The Blob container name that you will create

	# Connect to your Azure account and selec the current subscription
	Add-AzureAccount # The connection will expire in a few hours.
	Select-AzureSubscription $subscriptionName #only required if you have multiple subscriptions
	
	# Create a storage context object
	$storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext 


##<a id="addressing"></a>Archivos de dirección en almacenamiento de blobs

El esquema URI para obtener acceso a los archivos del almacenamiento de blobs es: 

	wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


> [WACOM.NOTE] La sintaxis para enviar los archivos al emulador de almacenamiento (que se ejecuta en el emulador de HDInsight) es <i>wasb://&lt;NombreDeContenedor&gt;@storageemulator.</i>.



El esquema URI proporciona acceso sin cifrar con el prefijo *wasb:* y acceso cifrado SSL con *wasbs*. Se recomienda usar *wasbs* siempre que sea posible, incluso al obtener acceso a los datos que residen en el mismo centro de datos de Azure.
	
El valor de &lt;BlobStorageContainerName&gt; identifica el nombre del contenedor de almacenamiento de blobs.
El valor de &lt;StorageAccountName&gt; identifica el nombre de la cuenta de almacenamiento de Azure. Se necesita el nombre completo de dominio (FQDN).
	
Si no se ha especificado ningún valor en &lt;BlobStorageContainerName&gt; ni en &lt;StorageAccountName&gt;, se usará el sistema de archivos predeterminado. Para los archivos del sistema de archivos predeterminado, puede usar una ruta relativa o absoluta. Por ejemplo, se puede hacer referencia al archivo hadoop-mapreduce-examples.jar que viene con los clústeres de HDInsight usando uno de los siguientes:

	wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	wasb:///example/jars/hadoop-mapreduce-examples.jar
	/example/jars/hadoop-mapreduce-examples.jar
	
> [WACOM.NOTE] El nombre de archivo es <i>hadoop-examples.jar</i> en clústeres de las versiones 1.6 y de 2.1 HDInsight.


La ruta &lt;path&gt; es el nombre de la ruta HDFS del archivo o el directorio. Como los contenedores de almacenamiento de blobs son solamente un almacén de pares clave-valor, no hay un sistema de archivos jerárquico real. Una "/" dentro de la clave de blob se interpreta como separador de directorios. Por ejemplo, el nombre del blob para *hadoop-mapreduce-examples.jar* es:

	example/jars/hadoop-mapreduce-examples.jar
	

##<a id="powershell"></a>Acceso a un blob usando Azure PowerShell

Consulte [Instalación y configuración de Azure PowerShell][powershell-install] para obtener información acerca de la instalación y la configuración de Azure PowerShell en su estación de trabajo. Puede usar la ventana de la consola de Azure PowerShell o PowerShell_ISE para ejecutar los cmdlets de PowerShell. 

Utilice el comando siguiente para incluir los cmdlets relacionados con el blob:

	Get-Command *blob*

![List of Blob-related PowerShell cmdlets.][img-hdi-powershell-blobcommands]


**Ejemplo de PowerShell para cargar un archivo**

Consulte [Carga de datos en HDInsight][hdinsight-upload-data].

**Ejemplo de PowerShell para descargar un archivo**

El script siguiente descarga un blob en bloques a la carpeta actual. Antes de ejecutar el script, cambie el directorio a una carpeta en la que tenga permiso de escritura. 


	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "example/data/sample.log" # The name of the blob to be downloaded.
	
	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours
	
	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	Write-Host "Download the blob ..." -ForegroundColor Green
	Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
	
	Write-Host "List the downloaded file ..." -ForegroundColor Green
	cat "./$blob"

**Ejemplo de PowerShell para eliminar un archivo**

En el siguiente script se muestra cómo eliminar un archivo.
	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "example/data/sample.log" # The name of the blob to be downloaded.
	
	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours
	
	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	Write-Host "Delete the blob ..." -ForegroundColor Green
	Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob 
	

**Ejemplo de PowerShell para incluir archivos en una carpeta**

El siguiente script muestra cómo enumerar archivos dentro de una "carpeta". En la siguiente muestra puede ver cómo usar el cmdlet Invoke-Hive para ejecutar el comando dfs ls para enumerar una carpeta.

	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blobPrefix = "example/data/"
	
	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours
	
	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Write-Host "List the files in $blobPrefix ..."
	Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix $blobPrefix

**Muestra de PowerShell para acceder a una cuenta de almacenamiento sin definir**
	
En esta muestra puede ver cómo enumerar una carpeta desde una cuenta de almacenamiento no definida durante el proceso de aprovisionamiento.
	$clusterName = "<HDInsightClusterName>"
	
	$undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
	$undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"
	
	$undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }
	
	Use-AzureHDInsightCluster $clusterName
	
	$defines = @{}
	$defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

	Invoke-Hive -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"
 
##<a id="nextsteps"></a>Pasos siguientes

En este artículo, ha aprendido a usar el almacenamiento de blobs con HDInsight y que el almacenamiento de blobs es un componente fundamental de HDInsight. Esto le permite crear soluciones de adquisición de datos de archivado escalable y a largo plazo con el almacenamiento de blobs de Azure; además de usar HDInsight para desbloquear la información que hay dentro de los datos almacenados.

Para obtener más información, consulte los artículos siguientes:

* [Introducción a HDInsight de Azure][hdinsight-get-started]
* [Carga de datos en HDInsight][hdinsight-upload-data]
* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Uso de Pig con HDInsight][hdinsight-use-pig]

[Powershell-install]: ../install-configure-powershell/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/

[Powershell-install]: ../install-configure-powershell/
[blob-storage-restAPI]: http://msdn.microsoft.com/en-us/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage-create-storage-account/

[img-hdi-powershell-blobcommands]: ./media/hdinsight-use-blob-storage/HDI.PowerShell.BlobCommands.png 
[img-hdi-quick-create]: ./media/hdinsight-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-use-blob-storage/HDI.CustomCreateStorageAccount.png  

<!--HONumber=35.1-->
