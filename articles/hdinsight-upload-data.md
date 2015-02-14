<properties 
	pageTitle="Carga de datos para trabajos de Hadoop en HDInsight | Azure" 
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
	ms.date="11/12/2014" 
	ms.author="jgao"/>



# Carga de datos para trabajos de Hadoop en HDInsight

HDInsight de Azure proporciona un sistema de archivos distribuidos Hadoop (HDFS) completo a través del servicio de almacenamiento de blobs de Azure. Se ha diseñado como extensión HDFS para proporcionar una experiencia sin igual a los clientes al habilitar un conjunto completo de componentes del ecosistema Hadoop para poder operar directamente en los datos que administra. Tanto el almacenamiento de blobs de Azure como HDFS son sistemas de archivos diferentes que se han optimizado para el almacenamiento de datos y el cálculo en ellos. Para conocer las ventajas del uso del almacenamiento de blobs de Azure, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][hdinsight-storage]. 

Los clústeres de HDInsight de Azure se implementan normalmente para ejecutar trabajos de MapReduce y se anulan una vez terminados. El mantenimiento de datos en los clústeres de HDFS después de haber completado los cálculos supondría un alto coste para el almacenamiento de estos datos. El almacenamiento de blobs de Azure tiene una excelente disponibilidad, es altamente escalable, cuenta con una gran capacidad, un bajo coste y la opción de almacenamiento que se puede compartir para los datos que se van a procesar usando HDInsight. Almacenar los datos en un blob permite que los clústeres de HDInsight que se usan para los cálculos se lancen de forma segura y sin perder los datos. 

Se puede tener acceso al almacenamiento de blobs de Azure mediante [AzCopy][azure-azcopy], [Azure PowerShell][azure-powershell], [Biblioteca de cliente de almacenamiento de Azure para .NET][azure-storage-client-library] o a través de las herramientas del explorador. Estas son algunas de las herramientas disponibles:

* [Explorador de almacenamiento de Azure](http://azurestorageexplorer.codeplex.com/)
* [Cloud Storage Studio 2](http://www.cerebrata.com/Products/CloudStorageStudio/)
* [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer)
* [Azure Explorer](http://www.cloudberrylab.com/free-microsoft-azure-explorer.aspx)
* [Azure Explorer PRO](http://www.cloudberrylab.com/microsoft-azure-explorer-pro.aspx)

**Requisitos previos**

Tenga en cuenta los siguientes requisitos antes de empezar este artículo:

* Un clúster de HDInsight de Azure. Para obtener instrucciones, consulte [Introducción a HDInsight de Azure][hdinsight-get-started] o [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision].

## En este artículo

* [Carga de datos en el almacenamiento de blobs usando AzCopy](#azcopy)
* [Carga de datos en el almacenamiento de blobs usando Azure PowerShell](#powershell)
* [Carga de datos en el almacenamiento de blobs usando el explorador de almacenamiento de Azure](#storageexplorer)
* [Carga de datos en el almacenamiento de blobs usando la línea de comandos de Hadoop](#commandline)
* [Importación de datos desde una base de datos SQL de Azure en el almacenamiento de blobs usando Sqoop](#sqoop)

## <a id="azcopy"></a>Carga de datos en el almacenamiento de blobs usando AzCopy##

AzCopy es una utilidad de línea de comandos que se ha diseñado para simplificar la tarea de transferir datos a una cuenta de almacenamiento de Azure y desde ella. Puede usarla como herramienta independiente o incorporarla a una aplicación que ya existe. [Descarga de AzCopy][azure-azcopy-download].

La sintaxis de AzCopy es la siguiente:

	AzCopy <Source> <Destination> [filePattern [filePattern...]] [Options]

Para obtener más información, consulte [AzCopy - Carga y descarga de archivos para blobs de Azure][azure-azcopy]

## <a id="powershell"></a>Carga de datos en el almacenamiento de blobs usando Azure PowerShell##

Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Puede usar Azure PowerShell para cargar datos en el almacenamiento de blobs, a fin de que los trabajos de MapReduce puedan procesar los datos. Para obtener información acerca de cómo configurar su estación de trabajo para que ejecute Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].

**Para cargar un archivo local en el almacenamiento de blobs**

1. Abra la ventana de la consola de Azure PowerShell como se indica en [Instalación y configuración de Azure PowerShell][powershell-install-configure].
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
		
3. Pegue el script en la ventana de la consola de Azure PowerShell para ejecutarlo.

Los contenedores del almacenamiento de blobs almacenan los datos como pares de clave/valor y no hay jerarquía de directorios. No obstante, el carácter "/" se puede usar en el nombre de la clave para que parezca que el archivo está almacenado dentro de una estructura de directorios. Por ejemplo, la clave de un blob puede ser  *input/log1.txt*. No hay directorios "input", pero dada la presencia del carácter "/" en el nombre de la clave, parece la ruta de un archivo. En el script anterior, puede dar una estructura de carpeta al archivo configurando la variable $blobname. Por ejemplo, *$blobname="myfolder/myfile.txt"*.

Al usar las herramientas de Azure Explorer, puede que vea algunos archivos de 0 bytes. Estos archivos tienen dos propósitos:

- En caso de que haya carpetas vacías, sirven como marcador de la existencia de la carpeta. El almacenamiento de blobs es lo suficientemente inteligente como para saber que si hay un blob que se llama foo/bar, es porque hay una carpeta llamada foo. Pero si quiere tener una carpeta vacía llamada foo, entonces la única forma de indicarlo es teniendo este archivo especial de 0 bytes dentro.
- Contienen metadatos especiales que necesita el sistema de archivos Hadoop, concretamente los permisos y los propietarios de las carpetas.








## <a id="storageexplorer"></a>Carga de datos en el almacenamiento de blobs usando el explorador de almacenamiento de Azure

El *Explorador de almacenamiento de Azure* es una herramienta útil para inspeccionar y modificar los datos de su almacenamiento de Azure. Se trata de una herramienta gratuita que se puede descargar de [http://azurestorageexplorer.codeplex.com/](http://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer").

Antes de usar la herramienta, debe saber el nombre y la clave de la cuenta de almacenamiento de Azure. Para obtener instrucciones acerca de cómo conseguir esta información, consulte la sección "Visualización, copia y regeneración de claves de acceso de almacenamiento" de [Creación, administración o eliminación de cuentas de almacenamiento].  

1. Ejecute el explorador de almacenamiento de Azure.

	![HDI.AzureStorageExplorer][image-azure-storage-explorer]

2. Haga clic en **Agregar cuenta**. Una vez que agregue la cuenta al explorador de almacenamiento de Azure, no tendrá que volver a realizar este paso. 

	![HDI.ASEAddAccount][image-ase-addaccount]

3. Escriba el **nombre de la cuenta de almacenamiento** y la **clave de la cuenta de almacenamiento** y, a continuación, haga clic en **Agregar cuenta de almacenamiento**. Puede agregar varias cuentas de almacenamiento y cada una de ellas aparecerá en una pestaña. 
4. En **Tipo de almacenamiento**, haga clic en **Blobs**.

	![HDI.ASEBlob][image-ase-blob]

5. En **Contenedor**, haga clic en el contenedor que esté asociado a su clúster de HDInsight. Cuando cree un clúster de HDInsight, debe especificar un contenedor.  Si no, el proceso de creación del clúster creará uno automáticamente.
6. En **Blob**, haga clic en **Cargar**.
7. Especifique el archivo que vaya a cargar y, a continuación, haga clic en **Abrir**.








































































## <a id="commandline"></a> Carga de datos en el almacenamiento de blobs usando la línea de comandos de Hadoop

Para usar la línea de comandos de Hadoop, primero debe conectarse al clúster usando un escritorio remoto. 



1. Inicie sesión en el [Portal de administración][azure-management-portal].
2. Haga clic en **HDINSIGHT**. Aparecerá una lista de los clústeres de Hadoop implementados.
3. Haga clic en el clúster de HDInsight en el que desee cargar los datos.
4. Haga clic en **CONFIGURACIÓN** en la parte superior de la página.
5. Haga clic en **HABILITAR REMOTO** si todavía no ha habilitado el escritorio remoto y siga las instrucciones.  Si no, continúe con el paso siguiente.
4. Haga clic en  **CONECTAR** en la parte inferior de la página.
7. Haga clic en **Open**.
8. Escriba sus credenciales y, a continuación, haga clic en **Aceptar**.
9. Haga clic en **Sí**.
10. En el escritorio, haga clic en **Línea de comandos Hadoop**.
12. El siguiente ejemplo indica cómo copiar el archivo davinci.txt desde el sistema de archivos local del nodo principal de HDInsight en el directorio /example/data.

		hadoop dfs -copyFromLocal C:\temp\davinci.txt /example/data/davinci.txt

	Como el sistema de archivos predeterminado está en el almacenamiento de blobs de Azure, /example/datadavinci.txt está en realidad en el almacenamiento de blobs de Azure.  También puede referirse al archivo como:

		wasb:///example/data/davinci.txt 

	o

		wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/example/data/davinci.txt

	Se requiere el nombre de dominio completo al usar *wasbs*.

13. Use el comando siguiente para incluir los archivos cargados:

		hadoop dfs -lsr /example/data


## <a id="sqoop"></a> Importación de datos a HDFS desde Base de datos SQL o SQL Server usando Sqoop

Sqoop es una herramienta diseñada para transferir datos entre Hadoop y las bases de datos relacionales. Puede usarla para importar datos desde un sistema de administración de bases de datos relacionales (RDBMS) como SQL, MySQL u Oracle en el sistema de archivos distribuidos Hadoop (HDFS), transformar los datos de Hadoop con MapReduce o Hive y, a continuación, exportar los datos en RDBMS. Para obtener más información, consulte el [manual del usuario de Sqoop][apache-sqoop-guide].

Antes de importar los datos, debe saber el nombre de la base de datos SQL de Azure, el nombre de la cuenta de la base de datos, la contraseña de la cuenta y el nombre de la base de datos. 

De forma predeterminada, una base de datos SQL de Azure permite realizar conexiones desde servicios de Azure como HDinsight. Si la configuración del firewall está deshabilitada, debe habilitarla en el Portal de administración de Azure. Para obtener instrucciones acerca de la creación de una base de datos SQL y la configuración de las reglas de firewall, consulte [Creación y configuración de una base de datos SQL][sqldatabase-create-configue]. 

El procedimiento siguiente usa PowerShell para enviar un trabajo de Sqoop. 

**Para importar datos a HDInsight usando Sqoop y PowerShell**

1. Abra la ventana de la consola de Azure PowerShell como se indica en [Instalación y configuración de Azure PowerShell][powershell-install-configure].
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

3. Pegue el script en la ventana de la consola de Azure PowerShell para ejecutarlo. Consulte [Introducción a HDInsight][hdinsight-get-started] para ver un ejemplo de PowerShell para recuperar el archivo de datos del almacenamiento de blobs de Azure.

Para obtener más información acerca de cómo usar Sqoop, consulte [Uso de Sqoop con HDInsight][hdinsight-use-sqoop].

## Pasos siguientes
Ahora que ya sabe cómo enviar datos a HDInsight, use los artículos siguientes para aprender a realizar el análisis:

* [Introducción a HDInsight de Azure][hdinsight-get-started]
* [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs]
* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Uso de Pig con HDInsight][hdinsight-use-pig]




[azure-management-portal]: https://manage.windowsazure.com
[azure-powershell]: http://msdn.microsoft.com/es-es/library/windowsazure/jj152841.aspx

[azure-storage-client-library]: /es-es/develop/net/how-to-guides/blob-storage/
[azure-create-storage-account]: ../storage-create-storage-account/
[azure-azcopy-download]: http://aka.ms/WaCopy
[azure-azcopy]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx

[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/

[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig
[hdinsight-provision]: ../hdinsight-provision-clusters/

[sqldatabase-create-configue]: ../sql-database-create-configure/

[apache-sqoop-guide]: http://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

[Powershell-install-configure]: ../install-configure-powershell/


[image-azure-storage-explorer]: ./media/hdinsight-upload-data/HDI.AzureStorageExplorer.png
[image-ase-addaccount]: ./media/hdinsight-upload-data/HDI.ASEAddAccount.png
[image-ase-blob]: ./media/hdinsight-upload-data/HDI.ASEBlob.png

<!--HONumber=42-->
