<properties linkid="manage-services-hdinsight-develop-hadoop-streaming-programs-for-hdinsight" urlDisplayName="" pageTitle="Develop C# Hadoop streaming programs for HDInsight | Azure" metaKeywords="hdinsight hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce" description="Learn how to develop Hadoop streaming MapReduce programs in C#, and how to deploy them to Azure HDInsight." metaCanonical="" services="hdinsight" documentationCenter="" title="Develop C# Hadoop streaming programs for HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Desarrollo de programas de streaming de Hadoop C# para HDInsight

Hadoop proporciona una API de streaming para MapReduce que le permite escribir mapas y reducir funciones en lenguajes distintos de Java. Este tutorial le guiará por un escenario integral, desde el desarrollo y la prueba de un programa de MapReduce de streaming de Hadoop usando C# en un emulador de HDInsight para ejecutar el trabajo de MapReduce en HDInsight de Azure y después recuperar los resultados.

**Requisitos previos:**

Antes de empezar este tutorial, debe contar con lo siguiente:

-   Instale el emulador de HDInsight de Azure. Para obtener más información, consulte [Introducción al emulador de HDInsight][Introducción al emulador de HDInsight].
-   Instale Azure PowerShell en el equipo emulador. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][Instalación y configuración de Azure PowerShell].
-   Obtenga una suscripción de Azure. Para obtener más información, consulte [Opciones de compra][Opciones de compra], [Ofertas para miembros][Ofertas para miembros] o [Evaluación gratuita][Evaluación gratuita].

## En este artículo

-   [Desarrollo de un programa de streaming de Hadoop para el recuento de palabras en C#][Desarrollo de un programa de streaming de Hadoop para el recuento de palabras en C#]
-   [Prueba del programa en el emulador][Prueba del programa en el emulador]
-   [Carga de los datos y las aplicaciones al almacenamiento de blobs de Azure][Carga de los datos y las aplicaciones al almacenamiento de blobs de Azure]
-   [Ejecución del programa de MapReduce en HDInsight de Azure][Ejecución del programa de MapReduce en HDInsight de Azure]
-   [Recuperación de los resultados de MapReduce][Recuperación de los resultados de MapReduce]
-   [Pasos siguientes][Pasos siguientes]

## <a name="develop"></a>Desarrollo de un programa de streaming de Hadoop para el recuento de palabras en C#

La solución para el recuento de palabras contiene dos proyectos de aplicaciones de consola: asignador y reductor. La aplicación del asignador transmite cada palabra a la consola y la aplicación del reductor cuenta el número de palabras que se transmiten desde un documento. Tanto el asignador como el reductor leen caracteres, línea por línea, a partir de la secuencia de entrada estándar (stdin) y escriben en la secuencia de salida estándar (stdout).

**Para crear la aplicación de consola en C#**

1.  Abra Visual Studio 2013.
2.  Haga clic en **ARCHIVO**, **Nuevo** y, a continuación, haga clic en **Proyecto**.
3.  Escriba o seleccione los valores siguientes:

	<table border="1">
	<tr><td>Campo</td><td>Valor</td></tr>
	<tr><td>Plantilla</td><td>Visual C#/Windows/Aplicación de consola</td></tr>
	<tr><td>Nombre</td><td>WordCountMapper</td></tr>
	<tr><td>Location</td><td>C:\Tutorials</td></tr>
	<tr><td>Nombre de la solución</td><td>WordCount</td></tr>
	</table>

4.  Haga clic en **Aceptar** para crear el proyecto.

**Para crear el programa del asignador**

1.  En el Explorador de soluciones, haga clic con el botón secundario en **Program.cs** y, a continuación, haga clic en **Rename**.
2.  Cambie el nombre del archivo a **WordCountMapper.cs** y, a continuación, presione **ENTRAR**.
3.  Haga clic en **Sí** para confirmar el cambio de nombre de todas las referencias.
4.  Haga doble clic en **WordCountMapper.cs** para abrirlo.
5.  Agregue la siguiente instrucción using:

        using System.IO;

6.  Reemplace la función Main() por el código siguiente:

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

7.  Haga clic en **COMPILAR** y, a continuación, haga clic en **Compilar solución** para compilar el programa del asignador.

**Para crear el programa reductor**

1.  En Visual Studio 2013, haga clic **FILE**, haga clic en **Add** y, a continuación, haga clic en **New Project**.
2.  Escriba o seleccione los valores siguientes:

	<table border="1">
	<tr><td>Campo</td><td>Valor</td></tr>
	<tr><td>Plantilla</td><td>Visual C#/Windows/Aplicación de consola</td></tr>
	<tr><td>Nombre</td><td>WordCountReducer</td></tr>
	<tr><td>Location</td><td>C:\Tutorials\WordCount</td></tr>
	</table>
3.  Haga clic en **Aceptar** para crear el proyecto.
4.  En el Explorador de soluciones, haga clic con el botón secundario en **Program.cs** y, a continuación, haga clic en **Rename**.
5.  Cambie el nombre del archivo a **WordCountReducer.cs** y, a continuación, presione **ENTRAR**.
6.  Haga clic en **Sí** para confirmar el cambio de nombre de todas las referencias.
7.  Haga doble clic en **WordCountReducer.cs** para abrirlo.
8.  Agregue la siguiente instrucción using:

        using System.IO;

9.  Reemplace la función Main() por el código siguiente:

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

10. Haga clic en **COMPILAR** y, a continuación, haga clic en **Compilar solución** para compilar la solución.

Los ejecutables de las aplicaciones del asignador y reductor se encuentran en:

-   C:\\Tutorials\\WordCount\\WordCountMapper\\bin\\Debug\\WordCountMapper.exe
-   C:\\Tutorials\\WordCount\\WordCountReducer\\bin\\Debug\\WordCountReducer.exe

## <a name="test"></a>Prueba del programa en el emulador

Esta sección contiene los procedimientos siguientes:

1.  cargar datos en el HDFS del emulador
2.  cargar los programas del asignador y reductor al HDFS del emulador
3.  enviar un trabajo de MapReduce para el recuento de palabras
4.  comprobar el estado del trabajo
5.  recuperar los resultados del trabajo

De manera predeterminada, el emulador de HDInsight utiliza HDFS como sistema de archivos predeterminado. Opcionalmente, puede configurar el emulador de HDInsight para utilizar el almacenamiento de blobs de Azure. Para obtener más información, consulte [Introducción al emulador de HDInsight][1]. En esta sección, utilizará el comando copyFromLocal de HDFS para cargar los archivos. La siguiente sección muestra cómo cargar archivos con Azure PowerShell. Para otros métodos, consulte [Carga de datos en HDInsight][Carga de datos en HDInsight].

Este tutorial utiliza la siguiente estructura de carpetas:

<table border="1">
<tr><td>Carpeta</td><td>Nota:</td></tr>
<tr><td>\WordCount</td><td>La carpeta raíz para el proyecto del recuento de palabras. </td></tr>
<tr><td>\WordCount\Apps</td><td>La carpeta para los ejecutables de los programas asignador y reductor.</td></tr>
<tr><td>\WordCount\Input</td><td>La carpeta de los archivos de origen de MapReduce.</td></tr>
<tr><td>\WordCount\Output</td><td>La carpeta de los archivos de resultados de MapReduce.</td></tr>
<tr><td>\WordCount\MRStatusOutput</td><td>La carpeta de resultados del trabajo.</td></tr>
</table></br>

Este tutorial utiliza los archivos .txt ubicados en el directorio %hadoop\_home%.

> [WACOM.NOTE] Los comandos HDFS de Hadoop distinguen mayúsculas de minúsculas.

**Para copiar los archivos de texto al HDFS del emulador**

1.  Desde la ventana de la línea de comandos de Hadoop, ejecute el comando siguiente para crear un directorio para los archivos de entrada:

        hadoop fs -mkdir /WordCount/Input

    La ruta de acceso utilizada aquí es la ruta de acceso relativa. Es equivalente a la siguiente:

        hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2.  Ejecute el comando siguiente para copiar algunos archivos de texto en la carpeta de entrada en HDFS:

        hadoop fs -copyFromLocal %hadoop_home%\*.txt \WordCount\Input

3.  Ejecute el comando siguiente para incluir los archivos cargados:

        hadoop fs -ls \WordCount\Input

    Verá unos ocho archivos .txt.

**Para implementar los programas del asignador y reductor en el HDFS del emulador**

1.  Abra la línea de comandos de Hadoop desde el escritorio.
2.  Ejecute los comandos siguientes:

        hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe /WordCount/Apps/WordCountMapper.exe
        hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe /WordCount/Apps/WordCountReducer.exe

3.  Ejecute el comando siguiente para incluir los archivos cargados

        hadoop fs -lsr /WordCount/Apps

    Verá los dos archivos .exe.

**Para ejecutar un trabajo de MapReduce mediante HDInsight PowerShell**

1.  Abra Azure PowerShell. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][Instalación y configuración de Azure PowerShell].
2.  Ejecute los siguientes comandos para establecer las variables:

        $clusterName = "http://localhost:50111"

        $mrMapper = "WordCountMapper.exe"
        $mrReducer = "WordCountReducer.exe"
        $mrMapperFile = "/WordCount/Apps/WordCountMapper.exe"
        $mrReducerFile = "/WordCount/Apps/WordCountReducer.exe"
        $mrInput = "/WordCount/Input/"
        $mrOutput = "/WordCount/Output"
        $mrStatusOutput = "/WordCount/MRStatusOutput"

    El nombre del clúster del emulador de HDInsight es "http://localhost:50111".

3.  Ejecute los siguientes comandos para definir el trabajo de streaming:

        $mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
        $mrJobDef.Files.Add($mrMapperFile)
        $mrJobDef.Files.Add($mrReducerFile)

4.  Ejecute el siguiente comando para crear un objeto de credenciales:

        $creds = Get-Credential -Message "Enter password" -UserName "hadoop"

    Se le solicitará que escriba la contraseña. La contraseña puede ser cualquier cadena. El nombre de usuario debe ser "hadoop".

5.  Ejecute los siguientes comandos para enviar el trabajo de MapReduce y espere a que se complete:

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

    Por tanto, sabe que el identificador del trabajo es, por ejemplo, job-201311132317-0034.

**Para comprobar el estado del trabajo**

1.  En el escritorio, haga clic en **Hadoop MapReduce Status** o diríjase a **http://localhost:50030/jobtracker.jsp**.
2.  Encuentre el trabajo mediante el identificador correspondiente en una de las tres secciones siguientes: **Completed Jobs**, **Running Jobs**, **Retired Jobs**.
3.  Si un trabajo tiene errores, puede abrir los detalles del trabajo y encontrar información útil para la depuración.

**Para mostrar el resultado de HDFS**

1.  Abra la línea de comandos de Hadoop.
2.  Ejecute los comandos siguientes para ver el resultado:

        hadoop fs -ls /WordCount/Output/
        hadoop fs -cat /WordCount/Output/part-00000

    Puede anexar "|more" al final del comando para obtener la vista de la página.

## <span id="upload"></span></a>Carga de archivos de datos al almacenamiento de blobs de Azure

HDInsight de Azure usa el almacenamiento de blobs de Azure como sistema de archivos predeterminado. Puede configurar un clúster de HDInsight para utilizar almacenamiento de blobs adicional para los archivos de datos. En esta sección, creará una cuenta de almacenamiento y cargará los archivos de datos en el almacenamiento de blobs. Los archivos de datos son los archivos txt del directorio %hadoop\_home%.

**Para crear un almacenamiento de blobs y un contenedor**

1.  Abra Azure PowerShell.
2.  Configure las variables y, a continuación, ejecute los siguientes comandos:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"  
        $containerName = "<ContainerName>"
        $location = "<MicrosoftDataCenter>"  # For example, "East US"

3.  Ejecute el comando siguiente para crear una cuenta de almacenamiento y un contenedor de almacenamiento de blobs en la cuenta:

        # Select Azure subscription
        Select-AzureSubscription $subscriptionName

        # Create a storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location

        # Create a Blob storage container
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  
        New-AzureStorageContainer -Name $containerName -Context $destContext

4.  Ejecute los comandos siguientes para comprobar la cuenta de almacenamiento y el contenedor:

        Get-AzureStorageAccount -StorageAccountName $storageAccountName
        Get-AzureStorageContainer -Context $destContext

**Para cargar los archivos de datos**

1.  Abra Azure PowerShell.
2.  Configure las tres primeras variables y, a continuación, ejecute los siguientes comandos:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"  
        $containerName = "<ContainerName>"

        $localFolder = "c:\Hadoop\hadoop-1.1.0-SNAPSHOT"
        $destFolder = "WordCount/Input"

    Tenga en cuenta que la carpeta de los archivos de origen es **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT** y la carpeta de destino es **WordCount/Input**.

3.  Ejecute los comandos siguientes para obtener una lista de los archivos txt en la carpeta de archivos de origen:

        # Get a list of the txt files
        $filesAll = Get-ChildItem $localFolder
        $filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

4.  Ejecute los comandos siguientes para crear un objeto de contexto de almacenamiento:

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

5.  Ejecute los comandos siguientes para copiar los archivos:

        # Copy the file from local workstation to the Blob container        
        foreach ($file in $filesTxt){

            $fileName = "$localFolder\$file"
            $blobName = "$destFolder/$file"

            write-host "Copying $fileName to $blobName"

            Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
        }

6.  Ejecute el comando siguiente para incluir los archivos cargados:

        # List the uploaded files in the Blob storage container
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder

**Para cargar las aplicaciones para el recuento de palabras**

1.  Abra Azure PowerShell.
2.  Configure las tres primeras variables y, a continuación, ejecute los siguientes comandos:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"  
        $containerName = "<ContainerName>"

        $mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
        $reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
        $blobFolder = "WordCount/Apps"

    Tenga en cuenta que la carpeta de destino es **WordCount/Apps**.

3.  Ejecute los comandos siguientes para crear un objeto de contexto de almacenamiento:

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

4.  Ejecute los comandos siguientes para copiar las aplicaciones:

        Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
        Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

5.  Ejecute el comando siguiente para incluir los archivos cargados:

        # List the uploaded files in the Blob storage container
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

    Deberían aparecer ambos archivos.

## <a name="run"></a>Ejecución del programa de MapReduce en HDInsight de Azure

El siguiente script de PowerShell ejecuta las tareas siguientes:

1.  aprovisionar un clúster de HDInsight

    1.  crear una cuenta de almacenamiento que se utilizará como el sistema de archivos predeterminado del clúster de HDInsight
    2.  crear un contenedor de almacenamiento de blobs
    3.  crear un clúster de HDInsight

2.  enviar el trabajo de MapReduce

    1.  crear una definición de trabajo de MapReduce de streaming
    2.  enviar un trabajo de MapReduce
    3.  esperar hasta que se haya completado el trabajo
    4.  mostrar el error estándar
    5.  mostrar el resultado estándar

3.  eliminar el clúster

    1.  eliminar el clúster de HDInsight
    2.  eliminar la cuenta de almacenamiento utilizada como sistema de archivos predeterminado del clúster de HDInsight

**Para ejecutar el script de PowerShell**

1.  Abra el Bloc de notas.
2.  Copie y pegue el código siguiente:

        # The storage account and the HDInsight cluster variables
        $subscriptionName = "<AzureSubscriptionName>"
        $serviceNameToken = "<ServiceNameTokenString>"
        $storageAccountName_Data = "<TheDataStorageAccountName>"
        $containerName_Data = "<TheDataBlobStorageContainerName>"
        $location = "<MicrosoftDataCenter>"     ### must match the data storage account location
        $clusterNodes = 1

        $clusterName = $serviceNameToken + "hdicluster"

        $storageAccountName_Default = $serviceNameToken + "hdistore"
        $containerName_Default =  $serviceNameToken + "hdicluster"

        # The streaming MapReduce job variables
        $mrMapper = "WordCountMapper.exe"
        $mrReducer = "WordCountReducer.exe"
        $mrMapperFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountMapper.exe"
        $mrReducerFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountReducer.exe"
        $mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
        $mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
        $mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

        Select-AzureSubscription $subscriptionName

        #=============================
        # Create a storage account
        Write-Host "Create a storage account" -ForegroundColor Green
        New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location

        #=============================
        # Create a Blob storage container
        Write-Host "Create a Blob storage container" -ForegroundColor Green
        $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
        $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default

        New-AzureStorageContainer -Name $containerName_Default -Context $destContext

        #=============================
        # Create an HDInsight cluster
        Write-Host "Create an HDInsight cluster" -ForegroundColor Green
        $storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }

        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
            Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
            Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data

        New-AzureHDInsightCluster -Subscription $subscriptionName  -Name $clusterName -Location $location -Config $config

        #=============================
        # Create a streaming MapReduce job definition
        Write-Host "Create a streaming MapReduce job definition" -ForegroundColor Green

        $mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
        $mrJobDef.Files.Add($mrMapperFile)
        $mrJobDef.Files.Add($mrReducerFile)

        #=============================
        # Run a streaming MapReduce job
        Write-Host "Run a streaming MapReduce job" -ForegroundColor Green
        $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Subscription $subscriptionName -JobDefinition $mrJobDef 
        Wait-AzureHDInsightJob -Subscription $subscriptionName -Job $mrJob -WaitTimeoutInSeconds 3600 

        Get-AzureHDInsightJobOutput -Cluster $clusterName -Subscription $subscriptionName -JobId $mrJob.JobId -StandardError 
        Get-AzureHDInsightJobOutput -Cluster $clusterName -Subscription $subscriptionName -JobId $mrJob.JobId -StandardOutput

        #=============================
        # Delete the HDInsight cluster
        Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
        Remove-AzureHDInsightCluster -Name $clusterName -Subscription $subscriptionName 

        # Delete the storage account
        Write-Host "Delete the storage account" -ForegroundColor Green
        Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3.  Establezca las cuatro primeras variables en el script. $serviceNameToken se utilizará para el nombre del clúster de HDInsight, el nombre de la cuenta de almacenamiento y el nombre del contenedor de almacenamiento de blobs. Como el nombre del servicio debe tener de 3 a 24 caracteres y el script anexa una cadena de hasta 10 caracteres a los nombres, debe limitar la cadena a 14 caracteres o menos. Debe utilizar minúsculas en $serviceNameToken. $storageAccountName\_Data y $containerName\_Data constituyen la cuenta de almacenamiento y el contenedor que se utilizan para almacenar los archivos de datos y las aplicaciones. $location debe coincidir con la ubicación de la cuenta de almacenamiento de datos.
4.  Revise el resto de las variables.
5.  Guarde el archivo de script.
6.  Abra Azure PowerShell.
7.  Ejecute el comando siguiente para establecer la directiva de ejecución en remotesigned:

        PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8.  Cuando se le solicite, escriba el nombre de usuario y la contraseña para el clúster de HDInsight. Como eliminará el clúster al final del script y no necesitará más el nombre de usuario y la contraseña, estos pueden ser cualquier cadena. Si no desea que se le soliciten las credenciales, consulte [Working with Passwords, Secure Strings and Credentials in Windows PowerShell][Working with Passwords, Secure Strings and Credentials in Windows PowerShell]

Para ver un ejemplo del SDK .NET de HDInsight sobre el envío de trabajos de streaming de Hadoop, consulte [Envío de trabajos de Hadoop mediante programación][Envío de trabajos de Hadoop mediante programación].

## <a name="retrieve"></a>Recuperación del resultado del trabajo de MapReduce

En esta sección se muestra cómo descargar y mostrar los resultados. Para obtener más información sobre cómo mostrar los resultados en Excel, consulte [Conexión de Excel a HDInsight con Microsoft Hive ODBC Driver][Conexión de Excel a HDInsight con Microsoft Hive ODBC Driver] y [Conexión de Excel a HDInsight con Power Query][Conexión de Excel a HDInsight con Power Query].

**Para recuperar los resultados**

1.  Abra la ventana de Azure PowerShell.
2.  Establezca los valores y, a continuación, ejecute los siguientes comandos:

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<TheDataStorageAccountName>"
        $containerName = "<TheDataBlobStorageContainerName>"
        $blobName = "WordCount/Output/part-00000"

3.  Ejecute los siguientes comandos para crear un objeto de contexto de almacenamiento de Azure:

        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  

4.  Ejecute los comandos siguientes para descargar y mostrar el resultado:

        Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
        cat "./$blobName" | findstr "there"

## <span id="nextsteps"></span></a>Pasos siguientes

En este tutorial, ha aprendido a desarrollar un trabajo de MapReduce de streaming de Hadoop, a probar la aplicación en el emulador de HDInsight y a escribir un script PowerShell para aprovisionar un clúster de HDInsight y ejecutar un trabajo de MapReduce en el clúster. Para obtener más información, consulte los artículos siguientes:

-   [Introducción a HDInsight de Azure][Introducción a HDInsight de Azure]
-   [Introducción al emulador de HDInsight][Introducción al emulador de HDInsight]
-   [Desarrollo de programas MapReduce de Java para HDInsight][Desarrollo de programas MapReduce de Java para HDInsight]
-   [Uso del almacenamiento de blobs de Azure con HDInsight][Uso del almacenamiento de blobs de Azure con HDInsight]
-   [Administración de HDInsight con PowerShell][Administración de HDInsight con PowerShell]
-   [Carga de datos en HDInsight][Carga de datos en HDInsight]
-   [Uso de Hive con HDInsight][Uso de Hive con HDInsight]
-   [Uso de Pig con HDInsight][Uso de Pig con HDInsight]

  [Introducción al emulador de HDInsight]: ../hdinsight-get-started-emulator/
  [Instalación y configuración de Azure PowerShell]: ../install-configure-powershell/
  [Opciones de compra]: http://azure.microsoft.com/es-es/pricing/purchase-options/
  [Ofertas para miembros]: http://azure.microsoft.com/es-es/pricing/member-offers/
  [Evaluación gratuita]: http://azure.microsoft.com/es-es/pricing/free-trial/
  [Desarrollo de un programa de streaming de Hadoop para el recuento de palabras en C#]: #develop
  [Prueba del programa en el emulador]: #test
  [Carga de los datos y las aplicaciones al almacenamiento de blobs de Azure]: #upload
  [Ejecución del programa de MapReduce en HDInsight de Azure]: #run
  [Recuperación de los resultados de MapReduce]: #retrieve
  [Pasos siguientes]: #nextsteps
  [1]: ../hdinsight-get-started-emulator/#blobstorage
  [Carga de datos en HDInsight]: ../hdinsight-upload-data/
  [Working with Passwords, Secure Strings and Credentials in Windows PowerShell]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
  [Envío de trabajos de Hadoop mediante programación]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Conexión de Excel a HDInsight con Microsoft Hive ODBC Driver]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Conexión de Excel a HDInsight con Power Query]: ../hdinsight-connect-excel-power-query/
  [Introducción a HDInsight de Azure]: ../hdinsight-get-started/
  [Desarrollo de programas MapReduce de Java para HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Uso del almacenamiento de blobs de Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [Administración de HDInsight con PowerShell]: ../hdinsight-administer-use-powershell/
  [Uso de Hive con HDInsight]: ../hdinsight-use-hive/
  [Uso de Pig con HDInsight]: ../hdinsight-use-pig/
