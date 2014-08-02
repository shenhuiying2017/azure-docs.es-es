<properties  linkid="manage-services-hdinsight-submit-hadoop-jobs-programmatically" urlDisplayName="HDInsight Administration" pageTitle="Submit Hadoop jobs programmatically | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure, hive, mapreduce, HDInsight .NET SDK, powershell, submit mapreduce jobs, submit hive jobs, development, hadoop, apache" description="Learn how to programmatically submit Hadoop jobs to Azure HDInsight." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Submit Hadoop jobs programmatically" authors="jgao" />

# Envío de trabajos de Hadoop mediante programación

En este artículo, aprenderá a enviar trabajos de MapReduce y Hive mediante PowerShell y el SDK .NET de HDInsight.

**Requisitos previos:**

Antes de empezar este artículo, debe tener lo siguiente:

* Un clúster de HDInsight de Azure. Para obtener más información,
  consulte [Introducción a
  HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/) o
  [Aprovisionamiento de clústeres de
  HDInsight](/en-us/manage/services/hdinsight/provision-hdinsight-clusters/).
* Instale y configure Azure PowerShell. Para obtener más información,
  consulte [Instalación y configuración de Azure
  PowerShell](/en-us/documentation/articles/install-configure-powershell/).

## En este artículo

* [Envío de trabajos de MapReduce mediante
  PowerShell](#mapreduce-powershell)
* [Envío de trabajos de Hive mediante PowerShell](#hive-powershell)
* [Envío de trabajos de Sqoop mediante PowerShell](#sqoop-powershell)
* [Envío de trabajos de MapReduce mediante el SDK .NET de
  HDInsight](#mapreduce-sdk)
* [Envío de trabajos de Hive mediante el SDK .NET de
  HDInsight](#hive-sdk)
* [Pasos siguientes](#nextsteps)

## <a id="mapreduce-powershell" ></a> Envío de trabajos de MapReduce mediante PowerShell

Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Para obtener más información acerca del uso de PowerShell con HDInsight, consulte [Administración de HDInsight con PowerShell](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/).

MapReduce de Hadoop es un marco de software para escribir aplicaciones que procesan enormes cantidades de datos. Los clústeres de HDInsight incluyen un archivo jar, ubicado en *\example\jars\hadoop-examples.jar*, que contiene varios ejemplos de MapReduce. En la versión 3.0 de los clústeres de HDInsight, se ha cambiado el nombre de este archivo por hadoop-mapreduce-examples.jar. Uno de los ejemplos ilustra el recuento de la frecuencia de las palabras en los archivos de código fuente. En esta sesión, aprenderá a utilizar PowerShell desde una estación de trabajo para ejecutar un ejemplo de recuento de palabras. Para obtener más información acerca del desarrollo y la ejecución de trabajos de MapReduce, consulte [Uso de MapReduce con HDInsight](/en-us/manage/services/hdinsight/using-mapreduce-with-hdinsight/).

**Para ejecutar el programa MapReduce de recuento de palabras mediante
PowerShell**

1.  Abra **Azure PowerShell**. Para obtener instrucciones acerca de cómo
    abrir la ventana de la consola de Azure PowerShell, consulte
    [Instalación y configuración de Azure
    PowerShell](/en-us/documentation/articles/install-configure-powershell/).

2.  Configure estas dos variables mediante la ejecución de los
    siguientes comandos de PowerShell:
    
         $subscriptionName = ""   
         $clusterName = ""    
        
        
   La suscripción es la que utilizó para crear el clúster de HDInsight. Y el clúster de HDInsight es el que debe utilizar para ejecutar el trabajo de MapReduce.
          
          
3.  Ejecute los siguientes comandos para crear una definición del trabajo de MapReduce:

         # Definir el trabajo de MapReduce para el recuento de palabras
         $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
        
        
   Hay dos argumentos. El primero es el nombre del archivo de origen y, el segundo, la ruta de acceso del archivo de salida. Para obtener más información acerca del prefijo wasb, consulte Uso del almacenamiento de blobs de Azure con HDInsight.
          
          
4.  Ejecute el siguiente comando para ejecutar el trabajo de MapReduce:

         # Enviar el trabajo de MapReduce
         Select-AzureSubscription $subscriptionName
         $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 
        
        
   Además de la definición de trabajo de MapReduce, también debe proporcionar el nombre del clúster de HDInsight en el que desea ejecutar el trabajo de MapReduce. 
          
          
5. Ejecute el siguiente comando para comprobar la finalización del trabajo de MapReduce:

         # Esperar hasta que se haya completado el trabajo
         Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 
        
          
          
6.  Ejecute el siguiente comando para comprobar posibles errores al ejecutar el trabajo de MapReduce:	

         # Obtener el resultado de error estándar del trabajo
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 
        
        
   En la captura de pantalla siguiente se muestra el resultado de una ejecución correcta. En caso contrario, aparecerán mensajes de error.
        
      ![HDI.GettingStarted.RunMRJob][image-hdi-gettingstarted-runmrjob]    
          
        
        
    **Para recuperar los resultados del trabajo de MapReduce**
        
        
 1.  Abra Azure PowerShell.         
 2.  Configure estas tres variables mediante la ejecución de los siguientes comandos de PowerShell:
        
         
         $subscriptionName = ""
         $storageAccountName = ""
         $containerName = ""			
        
        
   La cuenta de almacenamiento de Azure es la que especificó durante el aprovisionamiento de los clústeres de HDInsight. Esta se usa para hospedar el contenedor de blobs utilizado como sistema de archivos predeterminado del clúster de HDInsight.  El nombre del contenedor de almacenamiento de blobs suele coincidir con el del clúster de HDInsight, a menos que se especifique un nombre distinto durante el aprovisionamiento del clúster.
          
          
3.  Ejecute los siguientes comandos para crear un objeto de contexto de almacenamiento de Azure:

         # Crear el objeto de contexto de la cuenta de almacenamiento
         Select-AzureSubscription $subscriptionName
         $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
         $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
        
        
  Select-AzureSubscription se usa para establecer la suscripción actual en caso de tener varias y no usar la suscripción predeterminada. 
          
          
4.  Ejecute el siguiente comando para descargar el resultado del trabajo de MapReduce del contenedor de blobs a la estación de trabajo:

         # Obtener el contenido del blob
         Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
        
        
   example/data/WordCountOutput es la carpeta de salida especificada al ejecutar el trabajo de MapReduce. part-r-00000 es el nombre de archivo predeterminado para el resultado del trabajo de MapReduce.  El archivo se descargará a la misma estructura de carpetas de la carpeta local. Por ejemplo, en la captura de pantalla siguiente, la carpeta actual es la carpeta raíz C.  El archivo se descargará en la carpeta *C:\example\data\WordCountOutput*.
          
          
5.  Ejecute el siguiente comando para imprimir el archivo de salida del trabajo de MapReduce:
        
             cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"
 ![HDI.GettingStarted.MRJobOutput][image-hdi-gettingstarted-mrjoboutput]
        
  El trabajo de MapReduce genera un archivo denominado part-r-00000 con las palabras y los recuentos.  El script usa el comando findstr para enumerar todas las palabras que contienen "there".
          
        
        
        
   >[WACOM.NOTE] Si se abre en el Bloc de notas ./example/data/WordCountOutput/part-r-00000, resultado de varias líneas de un trabajo de MapReduce, observará que los saltos de línea no se representan correctamente. Se espera que esto sea así.
        
        
##  Envío de trabajos de Hive mediante PowerShell
   Apache Hive ofrece el modo de ejecutar un trabajo de MapReduce mediante un lenguaje de scripting de tipo SQL, llamado HiveQL, que se puede aplicar al resumen, la consulta y el análisis de grandes volúmenes de datos. 
        
   Los clústeres de HDInsight incluyen un ejemplo de tabla de Hive con nombre hivesampletable. En esta sesión, utilizará PowerShell para ejecutar un trabajo de Hive con el fin de obtener un listado de algunos datos de la tabla de Hive. 
        
   **Para ejecutar un trabajo de Hive mediante PowerShell**
        
        
1.   Abra Azure PowerShell. Para obtener instrucciones acerca de cómo abrir la ventana de la consola de Azure PowerShell, consulte Instalación y configuración de Azure PowerShell.
          
          
2.   Establezca las dos primeras variables en los comandos siguientes y, a continuación, ejecútelos:
        
         $subscriptionName = ""   
         $clusterName = ""             
         $querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"
        
        
   $querystring es la consulta de HiveQL.
          
          
3. Ejecute los siguientes comandos para seleccionar la suscripción de Azure y el clúster para ejecutar el trabajo de Hive:
        
             Select-AzureSubscription -SubscriptionName $subscriptionName
        
          
          
4.  Envío del trabajo de Hive:
        
         Use-AzureHDInsightCluster $clusterName
         Invoke-Hive -Query $queryString
        
        
   Puede utilizar el modificador -File para especificar un archivo de scripts de HiveQL en HDFS.
          
        
        
Para obtener más información acerca de Hive, consulte Uso de Hive con HDInsight.
        
 ## Envío de trabajos de Sqoop mediante PowerShell
        
 Consulte Uso de Sqoop con HDInsight.
        
  Envío de trabajos de MapReduce mediante el SDK .NET de HDInsight
  El SDK .NET de HDInsight proporciona bibliotecas de cliente .NET que facilitan el trabajo con los clústeres de HDInsight en .NET. Los clústeres de HDInsight incluyen un archivo jar, ubicado en \example\jars\hadoop-examples.jar, que contiene varios ejemplos de MapReduce. Uno de los ejemplos ilustra el recuento de la frecuencia de las palabras en los archivos de código fuente. En esta sesión, aprenderá a crear una aplicación .NET para ejecutar un ejemplo de recuento de palabras. Para obtener más información acerca del desarrollo y la ejecución de trabajos de MapReduce, consulte Uso de MapReduce con HDInsight.
        
  Los siguientes procedimientos son necesarios para aprovisionar un clúster de HDInsight con el SDK:
        
        
-     Instalar el SDK .NET de HDInsight
-     Crear una aplicación de consola
-     Ejecutar la aplicación
        
        
        
Para instalar el SDK .NET de HDInsight: puede instalar la última compilación publicada del SDK en NuGet. Las instrucciones se mostrarán en el siguiente procedimiento.
        
Para crear una aplicación de consola de Visual Studio
        
   
1. Abra Visual Studio 2012.
1. En el menú Archivo, haga clic en Nuevo y, a continuación, haga clic en Proyecto.
1. En Nuevo proyecto, escriba o seleccione los siguientes valores:       
            
         
	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propiedad</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valor</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categoría</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Plantillas/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Plantilla</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Aplicación de consola</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nombre</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">SubmitMapReduceJob</td></tr>
	</table>
         
          
          
4. Haga clic en Aceptar para crear el proyecto.
          
          
5. En el menú Herramientas, haga clic en Library Package Manager y, a continuación, en Package Manager Console.
          
6. Ejecute los siguientes comandos en la consola para instalar los paquetes.
        
             Install-Package Microsoft.Azure.Management.HDInsight
        
        
   Este comando agrega las bibliotecas y referencias .NET a los paquetes para el proyecto de Visual Studio actual. La versión debe ser la 0.11.0.1 o posterior.
          
          
7. En el Explorador de soluciones, haga doble clic en Program.cs para abrirlo.
          
          
8. Agregue la siguiente instrucción en la parte superior del archivo:
        
         using System.IO;
         using System.Threading;
         using System.Security.Cryptography.X509Certificates;
        	
         using Microsoft.WindowsAzure.Storage;
         using Microsoft.WindowsAzure.Storage.Blob;
        	
         using Microsoft.Azure.Management.HDInsight;
         using Microsoft.Hadoop.Client;
        
          
          
9. Agregue la siguiente definición de función a la clase. Esta función se utiliza para esperar a que se haya completado el trabajo de Hadoop.
        
             private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
         {
             JobDetails jobInProgress = client.GetJob(jobResults.JobId);
             while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
             {
                 jobInProgress = client.GetJob(jobInProgress.JobId);
                 Thread.Sleep(TimeSpan.FromSeconds(10));
             }
         }
        
          
          
10. En la función Main(), copie y pegue el siguiente código:
        
        // Definir las variables
        string subscriptionID = "";
        string certFriendlyName = "";
        
        string clusterName = "";
        	
        string storageAccountName = "";
        string storageAccountKey = "";
        string containerName = "";
        
        
   Estas son todas las variables que necesita configurar para el programa. Puede obtener el nombre de la Suscripción de Azure en el Portal de administración de Azure. 
        
   Para obtener información acerca del certificado, consulte Crear y cargar un certificado de administración para Azure. Una forma sencilla de configurar el certificado es ejecutar los cmdlets de PowerShell Get-AzurePublishSettingsFile y Import-AzurePublishSettingsFile. Estos crearán y cargarán el certificado de administración automáticamente. Una vez ejecutados los cmdlets de PowerShell, puede abrir certmgr.msc desde la estación de trabajo y encontrar el certificado al expandir Personal/Certificates. El certificado creado mediante los cmdlets de PowerShell incluye Azure Tools en los campos Issued To e Issued By.
        
   El nombre de la cuenta de almacenamiento de Azure es la cuenta que especifica al aprovisionar el clúster de HDInsight. El nombre del contenedor predeterminado es el mismo que el del clúster de HDInsight.
          
          
11. En la función Main(), anexe el siguiente código para definir el trabajo de MapReduce:
        
        // Definir el trabajo de MapReduce
        MapReduceJobCreateParameters mrJobDefinition = new MapReduceJobCreateParameters()
        {
            JarFile = "wasb:///example/jars/hadoop-examples.jar",
            ClassName = "wordcount"
        };
        
        mrJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt");
        mrJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput");
        
        
   Hay dos argumentos. El primero es el nombre del archivo de origen y, el segundo, la ruta de acceso del archivo de salida. Para obtener más información acerca del prefijo wasb, consulte Uso del almacenamiento de blobs de Azure con HDInsight.
          
          
            En la función Main(), anexe el siguiente código para crear un objeto JobSubmissionCertificateCredential:
        
            // Obtener el objeto de certificado del almacén de certificados mediante el nombre descriptivo para identificarlo: X509Store store = new X509Store(); store.Open(OpenFlags.ReadOnly); X509Certificate2 cert = store.Certificates.Cast().First(item => item.FriendlyName == certFriendlyName); JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
          
          
13.  En la función Main(), anexe el siguiente código para ejecutar el trabajo y esperar a que se haya completado:
        
        // Crear un cliente hadoop para conectar con HDInsight
        var jobClient = JobSubmissionClientFactory.Connect(creds);
        
        // Ejecutar el trabajo de MapReduce
        JobCreationResults mrJobResults = jobClient.CreateMapReduceJob(mrJobDefinition);
        
        // Esperar a que se haya completado el trabajo
        WaitForJobCompletion(mrJobResults, jobClient);
        
          
          
14.  En la función Main(), anexe el siguiente código para imprimir el resultado del trabajo de MapReduce:
        
        // Imprimir el resultado del trabajo de MapReduce
        Stream stream = new MemoryStream();
        	
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey);
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        CloudBlobContainer blobContainer = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blockBlob = blobContainer.GetBlockBlobReference("example/data/WordCountOutput/part-r-00000");
        	
        blockBlob.DownloadToStream(stream);
        stream.Position = 0;
        	
        StreamReader reader = new StreamReader(stream);
        Console.WriteLine(reader.ReadToEnd());
        	
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadLine();
        
        
   La carpeta de destino se especifica al definir el trabajo de MapReduce. El nombre del archivo predeterminado es part-r-00000.
          
        
        
   **Para ejecutar la aplicación**
        
   Mientras la aplicación está abierta en Visual Studio, presione F5 para ejecutarla. Una ventana de consola se abrirá y mostrará el estado y el resultado de la aplicación. 
        
   Envío de trabajos de Hive mediante el SDK .NET de HDInsight
   Los clústeres de HDInsight incluyen un ejemplo de tabla de Hive con nombre hivesampletable. En esta sesión, creará una aplicación .NET para ejecutar un trabajo de Hive con el fin de obtener un listado de las tablas de Hive creadas en el clúster de HDInsight. Para obtener más información acerca del uso de Hive, consulte Uso de Hive con HDInsight.
        
 ## Los siguientes procedimientos son necesarios para aprovisionar un clúster de HDInsight con el SDK:
        
        
-   Instalar el SDK .NET de HDInsight
-   Crear una aplicación de consola
-   Ejecutar la aplicación
        
        
  Para instalar el SDK .NET de HDInsight: puede instalar la última compilación publicada del SDK en NuGet. Las instrucciones se mostrarán en el siguiente procedimiento.
        
**Para crear una aplicación de consola de Visual Studio**
               
1. Abra Visual Studio 2012.
         
1.  En el menú Archivo, haga clic en Nuevo y, a continuación, haga clic en Proyecto.
       
1.  En Nuevo proyecto, escriba o seleccione los siguientes valores:
        
	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propiedad</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valor</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categoría</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Plantillas/Visual C#/Windows</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Plantilla</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Aplicación de consola</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nombre</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">EnviarTrabajoDeHive</td></tr>
	</table>
          

4.  Haga clic en Aceptar para crear el proyecto.
          
          
5.  En el menú Herramientas, haga clic en Library Package Manager y, a continuación, en Package Manager Console.
          
          
6.  Ejecute los siguientes comandos en la consola para instalar los paquetes.
        
             Install-Package Microsoft.Azure.Management.HDInsight
        
        
 Este comando agrega las bibliotecas y referencias .NET a los paquetes para el proyecto de Visual Studio actual.
          
          
7.  En el Explorador de soluciones, haga doble clic en Program.cs para abrirlo.
          
          
8.  Agregue la siguiente instrucción en la parte superior del archivo:
        
         using System.IO;
         using System.Threading;
         using System.Security.Cryptography.X509Certificates;
        
         using Microsoft.Azure.Management.HDInsight;
         using Microsoft.Hadoop.Client;
        
          
          
9. Agregue la siguiente definición de función a la clase. Esta función se utiliza para esperar a que se haya completado el trabajo de Hadoop.
        
             private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
         {
             JobDetails jobInProgress = client.GetJob(jobResults.JobId);
             while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
             {
                 jobInProgress = client.GetJob(jobInProgress.JobId);
                 Thread.Sleep(TimeSpan.FromSeconds(10));
             }
         }
        
          
          
10.  En la función Main(), copie y pegue el siguiente código:
        
        // Definir las variables
        string subscriptionID = "";
        string clusterName = "";
        string certFriendlyName = "";		
        
        
   Estas son todas las variables que necesita configurar para el programa. Puede obtener el identificador de la suscripción de Azure desde el administrador del sistema. 
        
   Para obtener información acerca del certificado, consulte Crear y cargar un certificado de administración para Azure. Una forma sencilla de configurar el certificado es ejecutar los cmdlets de PowerShell Get-AzurePublishSettingsFile y Import-AzurePublishSettingsFile. Estos crearán y cargarán el certificado de administración automáticamente. Una vez ejecutados los cmdlets de PowerShell, puede abrir certmgr.msc desde la estación de trabajo y encontrar el certificado al expandir Personal/Certificates. El certificado creado mediante los cmdlets de PowerShell incluye Azure Tools en los campos Issued To e Issued By.
          
          
11.   En la función Main(), anexe el siguiente código para definir el trabajo de Hive:
        
        // Definir el trabajo de Hive
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            Query = "show tables;"
        };
        
        
   Puede utilizar el parámetro File para especificar un archivo de scripts de HiveQL en HDFS. Por ejemplo:
        
        // Definir el trabajo de Hive
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            File = "/user/admin/showtables.hql"
        };
        
          
          
            En la función Main(), anexe el siguiente código para crear un objeto JobSubmissionCertificateCredential:
        
            // Obtener el objeto de certificado del almacén de certificados mediante el nombre descriptivo para identificarlo: X509Store store = new X509Store(); store.Open(OpenFlags.ReadOnly); X509Certificate2 cert = store.Certificates.Cast().First(item => item.FriendlyName == certFriendlyName); JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
          
          
12.  En la función Main(), anexe el siguiente código para ejecutar el trabajo y esperar a que se haya completado:
        
        // Enviar el trabajo de Hive
        var jobClient = JobSubmissionClientFactory.Connect(creds);
        JobCreationResults jobResults = jobClient.CreateHiveJob(hiveJobDefinition);
        
        // Esperar a que se haya completado el trabajo
        WaitForJobCompletion(jobResults, jobClient);
        
          
          
14. En la función Main(), anexe el siguiente código para imprimir el resultado del trabajo de Hive:
        
        // Imprimir el resultado del trabajo de Hive
        System.IO.Stream stream = jobClient.GetJobOutput(jobResults.JobId);
        
        StreamReader reader = new StreamReader(stream);
        Console.WriteLine(reader.ReadToEnd());
        
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadLine();
        
          
        
        
**Para ejecutar la aplicación**
        
  Mientras la aplicación está abierta en Visual Studio, presione F5 para ejecutarla. Una ventana de consola se abrirá y mostrará el estado de la aplicación. El resultado debe ser:
        
        hivesampletable
        
        
## Pasos siguientes
 En este artículo, ha aprendido a aprovisionar un clúster de HDInsight de varias formas. Para obtener más información, consulte los artículos siguientes:
        
        
-           Introducción a HDInsight de Azure
-           Aprovisionamiento de clústeres de HDInsight
-           Administración de HDInsight con PowerShell
-           Documentación de referencia de los cmdlets de HDInsight
-           Uso de Hive con HDInsight
-           Uso de Pig con HDInsight

[hdinsight-sqoop]: ../hdinsight-use-sqoop/



[azure-certificate]: http://msdn.microsoft.com/en-us/library/windowsazure/gg551722.aspx
[azure-management-portal]: http://manage.windowsazure.com/

[hdinsight-provision]: /en-us/manage/services/hdinsight/provision-hdinsight-clusters/
[hdinsight-mapreduce]: /en-us/manage/services/hdinsight/using-mapreduce-with-hdinsight/
[hdinsight-hive]:/en-us/manage/services/hdinsight/using-hive-with-hdinsight/
[hdinsight-pig]: /en-us/manage/services/hdinsight/using-pig-with-hdinsight/
[hdinsight-getting-started]: /en-us/manage/services/hdinsight/get-started-hdinsight/
[hdinsight-storage]: /en-us/manage/services/hdinsight/howto-blob-store/
[hdinsight-admin-powershell]: /en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/
[hdinsight-configure-powershell]: /en-us/manage/services/hdinsight/install-and-configure-powershell-for-hdinsight/ 
[hdinsight-powershell-reference]: http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx

[Powershell-install-configure]: /en-us/documentation/articles/install-configure-powershell/

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png 
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/
