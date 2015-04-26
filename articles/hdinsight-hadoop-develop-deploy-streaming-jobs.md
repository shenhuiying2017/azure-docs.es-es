<properties 
	pageTitle="Desarrollo de programas de streaming de Hadoop C# para HDInsight | Azure" 
	description="Aprenda a desarrollar programas de MapReduce de streaming de Hadoop en C# y a implementarlos en HDInsight de Azure." 
	services="hdinsight" 
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
	ms.date="09/25/2014" 
	ms.author="jgao"/>



# Desarrollo de programas de streaming de Hadoop C# para HDInsight

Hadoop proporciona una API de streaming para MapReduce que le permite escribir mapas y reducir funciones en lenguajes distintos de Java. Este tutorial le guiará por un escenario de recuento de palabras en el que se cuentan las repeticiones de una palabra determinada en los datos de entrada que proporcione. La siguiente ilustración muestra cómo el marco de MapReduce realiza un recuento de palabras.

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

En este tema se muestra cómo realizar las siguientes acciones:

- Desarrollo y prueba de un programa de MapReduce de streaming de Hadoop con C# en un emulador de HDInsight
- Ejecución del mismo trabajo de MapReduce en HDInsight de Azure 
- Recuperación de los resultados del trabajo de MapReduce

**Requisitos previos:**

Antes de empezar este tutorial, debe haber realizado lo siguiente:

- Instale el emulador de HDInsight de Azure. Para obtener más información, consulte [Introducción al emulador de HDInsight][hdinsight-get-started-emulator].
- Instale Azure PowerShell en el equipo emulador. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].
- Obtenga una suscripción de Azure. Para obtener instrucciones, consulte [Opciones de compra][azure-purchase-options], [Ofertas para miembros][azure-member-offers], o [Prueba gratuita][azure-free-trial].

##En este artículo

- [Desarrollo de un programa de streaming de Hadoop para el recuento de palabras en C#](#develop)
- [Prueba del programa en el emulador](#test)
- [Carga de los datos y las aplicaciones al almacenamiento de blobs de Azure](#upload)
- [Ejecución del programa de MapReduce en HDInsight de Azure](#run)
- [Recuperación de los resultados de MapReduce](#retrieve)
- [Pasos siguientes](#nextsteps)

##<a name="develop"></a>Desarrollo de un programa de streaming de Hadoop para el recuento de palabras en C#

La solución para el recuento de palabras contiene dos proyectos de aplicaciones de consola: asignador y reductor: La aplicación del asignador transmite cada palabra a la consola y la aplicación del reductor cuenta el número de palabras que se transmiten desde un documento. Tanto el asignador como el reductor leen caracteres, línea por línea, a partir de la secuencia de entrada estándar (stdin) y escriben en la secuencia de salida estándar (stdout).

**Para crear la aplicación de consola en C#**

1. Abra Visual Studio 2013.
2. Haga clic en **ARCHIVO**, **Nuevo** y, a continuación, haga clic en **Proyecto**.
3. Escriba o seleccione los valores siguientes:

	<table border="1">
	<tr><td>Campo</td><td>Valor</td></tr>
	<tr><td>Plantilla</td><td>Visual C#/Windows/Aplicación de consola</td></tr>
	<tr><td>Nombre</td><td>WordCountMapper</td></tr>
	<tr><td>Ubicación</td><td>C:\Tutorials</td></tr>
	<tr><td>Nombre de la solución</td><td>WordCount</td></tr>
	</table>
	
4. Haga clic en **Aceptar** para crear el proyecto.

**Para crear el programa del asignador**

5. En el Explorador de soluciones, haga clic con el botón secundario en **Program.cs** y, a continuación, haga clic en **Cambiar nombre**.
6. Cambie el nombre del archivo a **WordCountMapper.cs** y, a continuación, presione **ENTRAR**.
7. Haga clic en **Sí** para confirmar el cambio de nombre de todas las referencias.
8. Haga doble clic en **WordCountMapper.cs** para abrirlo.
9. Agregue la siguiente instrucción using:

		using System.IO;

10. Reemplace la función Main() por el código siguiente:

		static void Main(string[] args)
		{
		    if (args.Length > 0)
		    {
		        Console.SetIn(new StreamReader(args[0]));
		    }
		
		    string line;
		    string[] words;
		
		    while ((line = Console.ReadLine()) != null)
		    {
		        words = line.Split(' ');
		
		        foreach (string word in words)
		            Console.WriteLine(word.ToLower());
		    }
		}

11. Haga clic en **COMPILAR** y, a continuación, haga clic en **Compilar solución** para compilar el programa del asignador.	


**Para crear el programa reductor**

1. En Visual Studio 2013, haga clic **ARCHIVO**, haga clic en **Agregar** y, a continuación, haga clic en **Nuevo proyecto**.
2. Escriba o seleccione los valores siguientes:

	<table border="1">
	<tr><td>Campo</td><td>Valor</td></tr>
	<tr><td>Plantilla</td><td>Visual C#/Windows/Aplicación de consola</td></tr>
	<tr><td>Nombre</td><td>WordCountReducer</td></tr>
	<tr><td>Ubicación</td><td>C:\Tutorials\WordCount</td></tr>
	</table>
3. Desactive la casilla **Crear directorio para la solución** y haga clic en **Aceptar** para crear el proyecto.
4. En el Explorador de soluciones, haga clic con el botón secundario en **Program.cs** y, a continuación, haga clic en **Cambiar nombre**.
5. Cambie el nombre del archivo a **WordCountReducer.cs** y, a continuación, presione **ENTRAR**.
7. Haga clic en **Sí** para confirmar el cambio de nombre de todas las referencias.
8. Haga doble clic en **WordCountReducer.cs** para abrirlo.
9. Agregue la siguiente instrucción using:

		using System.IO;

10. Reemplace la función Main() por el código siguiente:

		static void Main(string[] args)
		{
		    string word, lastWord = null;
		    int count = 0;
		
		    if (args.Length > 0)
		    {
		        Console.SetIn(new StreamReader(args[0]));
		    }
		
		    while ((word = Console.ReadLine()) != null)
		    {
		        if (word != lastWord)
		        {
		            if(lastWord != null)
		                Console.WriteLine("{0}[{1}]", lastWord, count);
		
		            count = 1;
		            lastWord = word;
		        }
		        else
		        {
		            count += 1; 
		        }
		    }
		    Console.WriteLine(count);
		}

11. Haga clic en **COMPILAR** y, a continuación, en **Compilar solución** para compilar el programa del reductor.	

Los ejecutables de las aplicaciones del asignador y reductor se encuentran en:

- C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe
- C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe


##<a name="test"></a>Prueba del programa en el emulador

Lleve a cabo las siguientes acciones para probar el programa en el emulador:

1. Carga de datos en el HDFS del emulador
2. Carga de las aplicaciones del asignador y del reductor al HDFS del emulador
3. Envío de un trabajo de recuento de palabras de MapReduce
4. Comprobación del estado del trabajo
5. Recuperación de los resultados del trabajo

De manera predeterminada, el emulador de HDInsight utiliza HDFS como sistema de archivos predeterminado.  Opcionalmente, puede configurar el emulador de HDInsight para utilizar el almacenamiento de blobs de Azure. Para obtener más información, consulte [Introducción al emulador de HDInsight][hdinsight-emulator-wasb]. En esta sección, utilizará el comando *copyFromLocal* de HDFS para cargar los archivos. La siguiente sección muestra cómo cargar archivos con Azure PowerShell. Para otros métodos, consulte [Carga de datos en HDInsight][hdinsight-upload-data].

Este tutorial utiliza la siguiente estructura de carpetas:

<table border="1">
<tr><td>Carpeta</td><td>Nota:</td></tr>
<tr><td>\WordCount</td><td>La carpeta raíz para el proyecto del recuento de palabras. </td></tr>
<tr><td>\WordCount\Apps</td><td>La carpeta para los ejecutables de los programas asignador y reductor.</td></tr>
<tr><td>\WordCount\Input</td><td>La carpeta de los archivos de origen de MapReduce.</td></tr>
<tr><td>\WordCount\Output</td><td>La carpeta de los archivos de resultados de MapReduce.</td></tr>
<tr><td>\WordCount\MRStatusOutput</td><td>La carpeta de resultados del trabajo.</td></tr>
</table></br>

Este tutorial utiliza los archivos .txt ubicados en el directorio %hadoop_home%.

> [AZURE.NOTE] Los comandos HDFS de Hadoop distinguen mayúsculas de minúsculas.

**Para copiar los archivos de texto al HDFS del emulador**

1. Desde la ventana de la línea de comandos de Hadoop, ejecute el comando siguiente para crear un directorio para los archivos de entrada:

		hadoop fs -mkdir /WordCount/
		hadoop fs -mkdir /WordCount/Input

	La ruta de acceso utilizada aquí es la ruta de acceso relativa. Es equivalente a la siguiente:

		hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2. Ejecute el comando siguiente para copiar algunos archivos de texto en la carpeta de entrada en HDFS:

		hadoop fs -copyFromLocal %hadoop_home%\share\doc\hadoop\common\*.txt \WordCount\Input

3. Ejecute el comando siguiente para incluir los archivos cargados:

		hadoop fs -ls \WordCount\Input

	


**Para implementar los programas del asignador y reductor en el HDFS del emulador**

1. Abra la línea de comandos de Hadoop desde el escritorio y cree la carpeta /Apps en HDFS.
		hadoop fs -mkdir /WordCount/Apps

2. Ejecute los comandos siguientes:

		hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe /WordCount/Apps/WordCountMapper.exe
		hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe /WordCount/Apps/WordCountReducer.exe

3. Ejecute el comando siguiente para incluir los archivos cargados

		hadoop fs -ls /WordCount/Apps

	Verá los dos archivos .exe.


**Para ejecutar un trabajo de MapReduce mediante HDInsight PowerShell**

1. Abra Azure PowerShell. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure]. 
3. Ejecute los siguientes comandos para establecer las variables:

		$clusterName = "http://localhost:50111"
		
		$mrMapper = "WordCountMapper.exe"
		$mrReducer = "WordCountReducer.exe"
		$mrMapperFile = "/WordCount/Apps/WordCountMapper.exe"
		$mrReducerFile = "/WordCount/Apps/WordCountReducer.exe"
		$mrInput = "/WordCount/Input/"
		$mrOutput = "/WordCount/Output"
		$mrStatusOutput = "/WordCount/MRStatusOutput"

	El nombre del clúster del emulador de HDInsight es "http://localhost:50111".  

4. Ejecute los siguientes comandos para definir el trabajo de streaming:

		$mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
		$mrJobDef.Files.Add($mrMapperFile)
		$mrJobDef.Files.Add($mrReducerFile)

5. Ejecute el siguiente comando para crear un objeto de credenciales:

		$creds = Get-Credential -Message "Enter password" -UserName "hadoop"

	Se le solicitará que escriba la contraseña.  La contraseña puede ser cualquier cadena.  El nombre de usuario debe ser "hadoop".

6. Ejecute los siguientes comandos para enviar el trabajo de MapReduce y espere a que se complete:
		
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Credential $creds -JobDefinition $mrJobDef
		Wait-AzureHDInsightJob -Credential $creds -job $mrJob -WaitTimeoutInSeconds 3600

	Cuando haya finalizado, obtendrá un resultado similar al siguiente:

		StatusDirectory : /WordCount/MRStatusOutput
		ExitCode        : 
		Name            : mrWordCountStreamingJob
		Query           : 
		State           : Completed
		SubmissionTime  : 11/15/2013 7:18:16 PM
		Cluster         : http://localhost:50111
		PercentComplete : map 100%  reduce 100%
		JobId           : job_201311132317_0034
		
	Puede ver el identificador del trabajo en la salida; por ejemplo, *job-201311132317-0034*. 

**Para comprobar el estado del trabajo**

1. En el escritorio, haga clic en **Hadoop YARN Status** o diríjase a **http://localhost:50030/jobtracker.jsp**.
2. Encuentre el trabajo mediante el identificador correspondiente en la categoría **EN EJECUCIÓN** o **FINALIZADO**. 
3. Si un trabajo tiene errores, puede encontrarlo en la categoría **CON ERROR**. También puede abrir los detalles del trabajo y encontrar información útil para la depuración.


**Para mostrar el resultado de HDFS**

1. Abra la línea de comandos de Hadoop.
2. Ejecute los comandos siguientes para ver el resultado:

		hadoop fs -ls /WordCount/Output/
		hadoop fs -cat /WordCount/Output/part-00000

	Puede anexar "|more" al final del comando para obtener la vista de la página.

##<a id="upload"></a>Carga de archivos de datos al almacenamiento de blobs de Azure
HDInsight de Azure usa el almacenamiento de blobs de Azure como sistema de archivos predeterminado. Puede configurar un clúster de HDInsight para utilizar almacenamiento de blobs adicional para los archivos de datos. En esta sección, creará una cuenta de almacenamiento y cargará los archivos de datos en el almacenamiento de blobs. Los archivos de datos son los archivos .txt del directorio %hadoop_home%\share\doc\hadoop\common.


**Para crear un almacenamiento de blobs y un contenedor**

1. Abra Azure PowerShell.
2. Configure las variables y, a continuación, ejecute los siguientes comandos:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"  
		$containerName = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

3. Ejecute el comando siguiente para crear una cuenta de almacenamiento y un contenedor de almacenamiento de blobs en la cuenta:

		# Select Azure subscription
		Select-AzureSubscription $subscriptionName
		
		# Create a storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location
				
		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName -Context $destContext

4. Ejecute los comandos siguientes para comprobar la cuenta de almacenamiento y el contenedor:

		Get-AzureStorageAccount -StorageAccountName $storageAccountName
		Get-AzureStorageContainer -Context $destContext

**Para cargar los archivos de datos**

1. En la ventana de Azure PowerShell, establezca los valores de la carpeta local y las carpetas de destino.

		$localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common"
		$destFolder = "WordCount/Input"

	Observe que la carpeta local de archivos de origen es **C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common**, y la carpeta de destino es **WordCount/Input**. La ubicación de origen es la ubicación de los archivos .txt en el emulador de HDInsight. El destino es la estructura de carpetas que se reflejará en el contenedor de blobs de Azure.

3. Ejecute los siguientes comandos para obtener una lista de los archivos .txt de la carpeta de archivos de origen:

		# Get a list of the txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}
		
5. Ejecute el siguiente fragmento para copiar los archivos:

		# Copy the file from local workstation to the Blob container        
		foreach ($file in $filesTxt){
		 
		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"
		
		    write-host "Copying $fileName to $blobName"
		
		    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
		}

6. Ejecute el comando siguiente para incluir los archivos cargados:

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder


**Para cargar la aplicación para el recuento de palabras**

1. En la ventana de Azure PowerShell, establezca las siguientes variables:

		$mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
		$reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
		$blobFolder = "WordCount/Apps"

	Observe que la carpeta de destino es **WordCount/Apps**, que es la estructura que se reflejará en el contenedor de blobs de Azure.

2. Ejecute los comandos siguientes para copiar las aplicaciones:

		Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
		Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

3. Ejecute el comando siguiente para incluir los archivos cargados:

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

	Deben aparecer los dos archivos de aplicación indicados aquí.


##<a name="run"></a>Ejecución del trabajo de MapReduce en HDInsight de Azure

En esta sección se incluye un script de PowerShell que realiza todas las tareas relacionadas con la ejecución de un trabajo de MapReduce. La lista de tareas incluye:

1. Aprovisionamiento de un clúster de HDInsight
	
	1. Creación de una cuenta de almacenamiento que se utilizará como sistema de archivos predeterminado del clúster de HDInsight
	2. Creación de un contenedor de almacenamiento de blobs 
	3. Creación de un clúster de HDInsight

2. Envío del trabajo de MapReduce

	1. Creación de una definición de trabajo de MapReduce de streaming
	2. Envío de un trabajo de MapReduce
	3. Espera a que se complete el trabajo
	4. Visualización del error estándar
	5. Visualización del resultado estándar

3. Eliminación del clúster

	1. Eliminación del clúster de HDInsight
	2. Eliminación de la cuenta de almacenamiento usada como sistema de archivos predeterminado del clúster de HDInsight


**Para ejecutar el script de PowerShell**

1. Abra el Bloc de notas.
2. Copie y pegue el código siguiente:
		
		# ====== STORAGE ACCOUNT AND HDINSIGHT CLUSTER VARIABLES ======
		$subscriptionName = "<AzureSubscriptionName>"
		$stringPrefix = "<StringForPrefix>"     ### prefix to cluster, storage account, and container names
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$location = "<MicrosoftDataCenter>"     ### must match the data storage account location
		$clusterNodes = 1
		
		$clusterName = $stringPrefix + "hdicluster"
		
		$storageAccountName_Default = $stringPrefix + "hdistore"
		$containerName_Default =  $stringPrefix + "hdicluster"

		# ====== THE STREAMING MAPREDUCE JOB VARIABLES ======
		$mrMapper = "WordCountMapper.exe"
		$mrReducer = "WordCountReducer.exe"
		$mrMapperFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountMapper.exe"
		$mrReducerFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountReducer.exe"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"
		
		Select-AzureSubscription $subscriptionName
		
		#====== CREATE A STORAGE ACCOUNT ======
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location
		
		#====== CREATE A BLOB STORAGE CONTAINER ======
		Write-Host "Create a Blob storage container" -ForegroundColor Green
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default
		
		New-AzureStorageContainer -Name $containerName_Default -Context $destContext
		
		#====== CREATE AN HDINSIGHT CLUSTER ======
		Write-Host "Create an HDInsight cluster" -ForegroundColor Green
		$storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data
		
		Select-AzureSubscription $subscriptionName
		New-AzureHDInsightCluster -Name $clusterName -Location $location -Config $config
		
		#====== CREATE A STREAMING MAPREDUCE JOB DEFINITION ======
		Write-Host "Create a streaming MapReduce job definition" -ForegroundColor Green
		
		$mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
		$mrJobDef.Files.Add($mrMapperFile)
		$mrJobDef.Files.Add($mrReducerFile)
		
		#====== RUN A STREAMING MAPREDUCE JOB ======
		Write-Host "Run a streaming MapReduce job" -ForegroundColor Green
		Select-AzureSubscription $subscriptionName
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
		Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 
		
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput
		
		#====== DELETE THE HDINSIGHT CLUSTER ======
		Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
		Select-AzureSubscription $subscriptionName
		Remove-AzureHDInsightCluster -Name $clusterName 
		
		#====== DELETE THE STORAGE ACCOUNT ======
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. Establezca las cuatro primeras variables en el script. **$stringPrefix** se usa para agregar la cadena especificada como prefijo al nombre del clúster de HDInsight, el nombre de la cuenta de almacenamiento y el nombre del contenedor de almacenamiento de blobs. Puesto que estos nombres deben tener entre 3 y 24 caracteres, asegúrese de que la cadena que especifique y los nombres que usa el script no superen el límite de caracteres del nombre. Solo debe utilizar minúsculas en $stringPrefix.

	 **$storageAccountName_Data** y **$containerName_Data** corresponden a la cuenta y el contenedor de almacenamiento que ya creó en los pasos anteriores. Por consiguiente, debe indicar los nombres de los mismos. Se usan para almacenar los archivos de datos y las aplicaciones. $location debe coincidir con la ubicación de la cuenta de almacenamiento de datos.

4. Revise el resto de las variables.
5. Guarde el archivo de script.
6. Abra Azure PowerShell.
7. Ejecute el comando siguiente para establecer la directiva de ejecución en remotesigned:

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. Cuando se le solicite, escriba el nombre de usuario y la contraseña para el clúster de HDInsight. Asegúrese de que la contraseña tiene como mínimo 10 caracteres, contiene una mayúscula, una minúscula, un número y un carácter especial. Si no desea que se le soliciten las credenciales, consulte [Trabajo con contraseñas, cadenas seguras y credenciales en Windows PowerShell][powershell-PSCredential]

Para ver un ejemplo del SDK .NET de HDInsight sobre el envío de trabajos de streaming de Hadoop, consulte [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs].


##<a name="retrieve"></a>Recuperación del resultado del trabajo de MapReduce
En esta sección se muestra cómo descargar y mostrar los resultados.  Para obtener más información sobre cómo mostrar los resultados en Excel, consulte [Conexión de Excel a HDInsight con Microsoft Hive ODBC Driver][hdinsight-ODBC] y [Conexión de Excel a HDInsight con Power Query][hdinsight-power-query].


**Para recuperar los resultados**

1. Abra la ventana de Azure PowerShell.
2. Establezca los valores y, a continuación, ejecute los siguientes comandos:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<TheDataStorageAccountName>"
		$containerName = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-00000"
	
3. Ejecute los siguientes comandos para crear un objeto de contexto de almacenamiento de Azure: 
		
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

4. Ejecute los comandos siguientes para descargar y mostrar el resultado:

		Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"

	

##<a id="nextsteps"></a>Pasos siguientes
En este tutorial, ha aprendido a desarrollar un trabajo de MapReduce de streaming de Hadoop, a probar la aplicación en el emulador de HDInsight y a escribir un script PowerShell para aprovisionar un clúster de HDInsight y ejecutar un trabajo de MapReduce en el clúster. Para obtener más información, consulte los artículos siguientes:

- [Introducción a HDInsight de Azure](hdinsight-get-started.md)
- [Introducción al emulador de HDInsight][hdinsight-get-started-emulator]
- [Desarrollo de programas MapReduce de Java para HDInsight][hdinsight-develop-mapreduce]
- [Uso del almacenamiento de blobs de Azure con HDInsight][hdinsight-storage]
- [Administración de HDInsight con PowerShell][hdinsight-admin-powershell]
- [Carga de datos en HDInsight][hdinsight-upload-data]
- [Uso de Hive con HDInsight][hdinsight-use-hive]
- [Uso de Pig con HDInsight][hdinsight-use-pig]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/

[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator/
[hdinsight-emulator-wasb]: ../hdinsight-get-started-emulator/#blobstorage
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-ODBC]: ../hdinsight-connect-excel-hive-ODBC-driver/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-wordcountdiagram]: ./media/hdinsight-hadoop-develop-deploy-streaming-jobs/HDI.WordCountDiagram.gif "MapReduce wordcount application flow"







<!--HONumber=42-->
