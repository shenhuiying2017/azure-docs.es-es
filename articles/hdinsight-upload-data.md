<properties 
	pageTitle="Carga de datos para trabajos de Hadoop en HDInsight | Microsoft Azure" 
	description="Aprenda a cargar datos en HDInsight y obtener acceso a ellos con el Explorador de almacenamiento de Azure, Azure PowerShell, la línea de comandos de Hadoop o Sqoop." 
	services="storage, hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="jgao"/>



#Carga de datos para trabajos de Hadoop en HDInsight

HDInsight de Azure ofrece un sistema de archivos distribuido de Hadoop (HDFS) completo a través del servicio de almacenamiento de blobs de Azure. Está diseñado como una extensión de HDFS para ofrecer una experiencia continua para los clientes. Habilita que el conjunto completo de componentes en el ecosistema de Hadoop opere directamente en los datos que administra. El almacenamiento de blobs de Azure y HDFS son sistemas de archivos diferentes que se han optimizado para el almacenamiento de datos y el cálculo en ellos. Para obtener más información sobre las ventajas del uso del almacenamiento de blobs de Azure, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][hdinsight-storage].

Los clústeres de HDInsight de Azure se implementan normalmente para ejecutar trabajos de MapReduce y dichos clústeres se anulan cuando los trabajos se completan. El mantenimiento de datos en los clústeres de HDFS después de que se hayan completado los cálculos supondría un alto coste para el almacenamiento de estos datos. El almacenamiento de blobs de Azure tiene una excelente disponibilidad, es altamente escalable, cuenta con una gran capacidad, un bajo coste y la opción de almacenamiento que se puede compartir para los datos que se van a procesar usando HDInsight. El almacenamiento de los datos en un blob permite que los clústeres de HDInsight que se usan para los cálculos se lancen de forma segura y sin perder los datos.

Se puede tener acceso al almacenamiento de blobs de Azure mediante [AzCopy][azure-azcopy], [Azure PowerShell][azure-powershell], [Biblioteca de cliente de almacenamiento de Azure para .NET][azure-storage-client-library], [la interfaz de la línea de comandos para Mac, Linux y Windows][xplatcli] o a través de las herramientas del explorador. Estas son algunas de las herramientas disponibles:

* [Explorador de almacenamiento de Azure](http://azurestorageexplorer.codeplex.com/)
* [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/)
* [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer)
* [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx)
* [Azure Explorer PRO](http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx)

##Requisitos previos

Tenga en cuenta el requisito siguiente antes de empezar:

* Un clúster de HDInsight de Azure. Para obtener instrucciones, consulte [Introducción a HDInsight de Azure][hdinsight-get-started] o [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision].


##<a id="azcopy"></a>Carga de datos en el almacenamiento de blobs de Azure mediante AzCopy##

AzCopy es una herramienta de la línea de comandos diseñada para simplificar la tarea de transferir datos dentro y fuera de una cuenta de almacenamiento de Azure. Puede usarla como una herramienta independiente o incorporarla a una aplicación existente. [Descarga de AzCopy.][azure-azcopy-download].

La sintaxis de AzCopy es la siguiente:

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Para obtener más información, consulte [AzCopy: carga y descarga de archivos para blobs de Azure][azure-azcopy].

##<a id="powershell"></a>Carga de datos en el almacenamiento de blobs de Azure mediante Azure PowerShell##

Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Puede usar Azure PowerShell para cargar datos en el almacenamiento de blobs de Azure, de forma que los trabajos de MapReduce puedan procesar los datos. Para obtener información sobre cómo configurar su estación de trabajo para que ejecute Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell](powershell-install-configure.md).

**Para cargar un archivo local en el almacenamiento de blobs de Azure**

1. Abra la consola de Azure PowerShell como se indica en [Instalación y configuración de Azure PowerShell](powershell-install-configure.md).
2. Configure los valores de las cinco primeras variables del script siguiente:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"
		
		$fileName ="<LocalFileName>"
		$blobName = "<BlobName>"
		
		# Get the storage account key
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		
		# Create the storage context object
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
		
		# Copy the file from local workstation to the Blob container		
		Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -context $destContext
		
3. Pegue el script en la consola de Azure PowerShell para ejecutarlo.

Los contenedores del almacenamiento de blobs de Azure almacenan los datos como pares de clave/valor y no hay jerarquía de directorios. No obstante, el carácter "/" se puede usar en el nombre de la clave para que parezca que el archivo está almacenado dentro de una estructura de directorios. Por ejemplo, la clave de un blob puede ser *input/log1.txt*. No hay directorios "input", pero dada la presencia del carácter "/" en el nombre de la clave, parece la ruta de un archivo. En el script anterior, puede dar una estructura de carpeta al archivo configurando la variable **$blobname**; por ejemplo, *$blobname="myfolder/myfile.txt"*.

Si usa las herramientas de Azure Explorer, puede que vea algunos archivos de 0 bytes. Estos archivos tienen dos propósitos:

- Si hay carpetas vacías, son una marca de la existencia de la carpeta. El almacenamiento de blobs de Azure es suficientemente inteligente para saber que si existe un blob que se llama foo/bar, es porque hay una carpeta llamada **foo**. Pero la única forma de indicar una carpeta vacía llamada **foo** es disponer este archivo especial de 0 bytes en su sitio.
- Contienen metadatos especiales que necesita el sistema de archivos de Hadoop, concretamente los permisos y los propietarios de las carpetas.


##<a id="xplatcli"></a>Carga de datos en el almacenamiento de blobs de Azure mediante la CLI de Azure

La CLI de Azure para Mac, Linux y Windows es una herramienta multiplataforma que le permite administrar los servicios de Azure. Para cargar datos en el almacenamiento de blobs de Azure, siga estos pasos:

1. [Instalación y configuración de la CLI de Azure para Mac, Linux y Windows](xplat-cli.md).

2. Abra un símbolo del sistema, bash u otro shell y use lo siguiente para autenticarse en su suscripción de Azure.

		azure login

	Cuando se le solicite, escriba el nombre de usuario y la contraseña de su suscripción.

3. Escriba el comando siguiente para enumerar las cuentas de almacenamiento de su suscripción:

		azure storage account list

4. Seleccione la cuenta de almacenamiento que contiene el blob con en el que quiere trabajar y use el comando siguiente para recuperar la clave de esta cuenta:

		azure storage account keys list <storage-account-name>

	Esto debería devolver las claves **Principal** y **Secundaria**. Copie el valor de la clave **Principal** ya que se usará en los pasos siguientes.

5. Use el comando siguiente para recuperar una lista de contenedores de blobs dentro de la cuenta de almacenamiento:

		azure storage container list -a <storage-account-name> -k <primary-key>

6. Use los comandos siguientes para cargar y descargar archivos en el blob:

	* Para cargar un archivo:

			azure storage blob upload -a <storage-account-name> -k <primary-key> <source-file> <container-name> <blob-name>

	* Para descargar un archivo:

			azure storage blob download -a <storage-account-name> -k <primary-key> <container-name> <blob-name> <destination-file>

> [AZURE.NOTE]Si siempre trabajará con la misma cuenta de almacenamiento, puede establecer las siguientes variables de entorno en lugar de especificar la cuenta y la clave para cada comando:
> 
> * **AZURE_STORAGE_ACCOUNT**: el nombre de la cuenta de almacenamiento.
> 
> * **AZURE_STORAGE_ACCESS_KEY**: la clave de la cuenta de almacenamiento.

##<a id="storageexplorer"></a>Carga de datos en el almacenamiento de blobs de Azure mediante el Explorador de almacenamiento de Azure

El *Explorador de almacenamiento de Azure* es una herramienta útil para inspeccionar y modificar los datos del almacenamiento de Azure. Se trata de una herramienta gratuita que se puede descargar de CodePlex: [Explorador de almacenamiento de Azure](http://azurestorageexplorer.codeplex.com/ "Explorador de almacenamiento de Azure").

Antes de usar la herramienta, debe saber el nombre y la clave de la cuenta de almacenamiento de Azure. Para ver instrucciones sobre cómo obtener esta información, consulte la sección "Visualización, copia y regeneración de claves de acceso de almacenamiento" de [Creación, administración o eliminación de una cuenta de almacenamiento][azure-create-storage-account].

1. Ejecute el explorador de almacenamiento de Azure.

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

2. Haga clic en **Agregar cuenta**. Una vez que agregue la cuenta al explorador de almacenamiento de Azure, no tendrá que volver a realizar este paso.

	![HDI.ASEAddAccount][image-ase-addaccount]

3. Escriba el **nombre de la cuenta de almacenamiento** y la **clave de la cuenta de almacenamiento** y, después, haga clic en **Agregar cuenta de almacenamiento**. Puede agregar varias cuentas de almacenamiento y cada una de ellas aparecerá en una pestaña.
4. En **Tipo de almacenamiento**, haga clic en **Blobs**.

	![HDI.ASEBlob][image-ase-blob]

5. En **Contenedor**, haga clic en el nombre del contenedor que esté asociado a su clúster de HDInsight. Cuando cree un clúster de HDInsight, debe especificar un contenedor. Si no, el proceso de creación del clúster creará uno automáticamente.
6. En **Blob**, haga clic en **Cargar**.
7. Especifique el archivo que quiere cargar y haga clic en **Abrir**.








































































##<a id="commandline"></a> Carga de datos en el almacenamiento de blobs de Azure mediante la línea de comandos de Hadoop

Para poder usar la línea de comandos de Hadoop, primero debe conectarse al clúster mediante Escritorio remoto.



1. Inicie sesión en el [Portal de Azure][azure-management-portal].
2. Haga clic en **HDINSIGHT**. Aparecerá una lista de los clústeres de Hadoop implementados.
3. Haga clic en el clúster de HDInsight en el que desee cargar los datos.
4. Haga clic en **CONFIGURACIÓN**, en la parte superior de la página.
5. Haga clic en **HABILITAR REMOTO** si todavía no ha habilitado Escritorio remoto y siga las instrucciones. Si no, continúe con el paso siguiente.
4. Haga clic en **CONECTAR**, en la parte inferior de la página.
7. Haga clic en **Abrir**.
8. Escriba sus credenciales y haga clic en **Aceptar**.
9. Haga clic en **Sí**.
10. En el escritorio, haga clic en  **Línea de comandos Hadoop**.
12. La siguiente muestra de código indica cómo copiar el archivo davinci.txt desde el sistema de archivos local del nodo principal de HDInsight en el directorio /example/data.

		hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt

	Como el sistema de archivos predeterminado está en el almacenamiento de blobs de Azure, /example/datadavinci.txt está en realidad en el almacenamiento de blobs de Azure. También puede referirse al archivo como:

		wasb:///example/data/davinci.txt 

	o

		wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

	Se necesita el nombre completo del dominio para usar *wasbs*.

13. Use el comando siguiente para incluir los archivos cargados:

		hadoop dfs -lsr /example/data


##<a id="sqoop"></a> Importación de datos en HDFS desde SQL Server o Base de datos SQL de Azure mediante Sqoop

Sqoop es una herramienta diseñada para transferir datos entre Hadoop y las bases de datos relacionales. Puede usarla para importar datos desde un sistema de administración de bases de datos relacionales (RDBMS), como SQL Server, MySQL u Oracle en el sistema de archivos distribuidos de Hadoop (HDFS), transformar los datos de Hadoop con MapReduce o Hive y, a continuación, exportar los datos en un RDBMS. Para obtener más información, consulte el [Manual del usuario de Sqoop][apache-sqoop-guide] (en inglés).

Antes de importar los datos, debe conocer el nombre del servidor que hospeda la base de datos SQL de Azure, el nombre de la cuenta de la base de datos, la contraseña de la cuenta y el nombre de la base de datos.

De forma predeterminada, una base de datos SQL de Azure permite conexiones desde servicios de Azure, como HDinsight de Azure. Si esta configuración del firewall está deshabilitada, debe habilitarla en el portal de Azure. Para obtener instrucciones sobre la creación de una base de datos SQL de Azure y la configuración de las reglas de firewall, consulte [Creación y configuración de una base de datos SQL][sqldatabase-create-configure].

El procedimiento siguiente usa Azure PowerShell para enviar un trabajo de Sqoop.

**Para importar datos en HDInsight mediante Sqoop y Azure PowerShell**

1. Abra la consola de Azure PowerShell como se indica en [Instalación y configuración de Azure PowerShell](powershell-install-configure.md).
2. Configure los valores de las ocho primeras variables del script siguiente:

		$subscriptionName = "<AzureSubscriptionName>"
		$clusterName = "<HDInsightClusterName>"
		
		$sqlDatabaseServerName = "<SQLDatabaseServerName>"
		$sqlDatabaseUserName = "<SQLDatabaseDatabaseName>"
		$sqlDatabasePassword = "<SQLDatabasePassword>"
		$sqlDatabaseDatabaseName = "<SQLDatabaseDatabaseName>"
		$tableName = "<SQLDatabaseTableName>"
		
		$hdfsOutputDir = "<OutputPath>"  # This is the HDFS path for the output file, for example "/lineItemData".
		
		Select-AzureSubscription $subscriptionName		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "import --connect jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseDatabaseName --table $tableName --target-dir $hdfsOutputDir -m 1" 

		$sqoopJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $sqoopJob.JobId -StandardOutput

3. Pegue el script en la consola de Azure PowerShell para ejecutarlo. Consulte [Introducción a HDInsight][hdinsight-get-started] para ver una muestra de recuperación del archivo de datos del almacenamiento de blobs de Azure.

Para obtener más información sobre el uso de Sqoop, consulte [Uso de Sqoop con HDInsight][hdinsight-use-sqoop].

## Pasos siguientes
Ahora que ya sabe cómo enviar datos a HDInsight, consulte los artículos siguientes para aprender a realizar el análisis:

* [Introducción a HDInsight de Azure][hdinsight-get-started]
* [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs]
* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Uso de Pig con HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://manage.windowsazure.com
[azure-powershell]: http://msdn.microsoft.com/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: storage-create-storage-account.md
[azure-azcopy-download]: storage-use-azcopy.md
[azure-azcopy]: storage-use-azcopy.md

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md

[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-get-started]: hdinsight-get-started.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[sqldatabase-create-configure]: sql-database-create-configure.md

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: powershell-install-configure.md

[xplatcli]: xplat-cli.md


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png


<!--HONumber=54-->