<properties linkid="manage-services-hdinsight-submit-hadoop-jobs" urlDisplayName="HDInsight Administration" pageTitle="Submit Hadoop jobs in HDInsight | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure, hive, mapreduce, HDInsight .NET SDK, powershell, submit mapreduce jobs, submit hive jobs, development, hadoop, apache" description="Learn how to submit Hadoop jobs to Azure HDInsight Hadoop." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Submit  Hadoop jobs in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Envío de trabajos de Hadoop en HDInsight

En este artículo, aprenderá a enviar trabajos de MapReduce y Hive mediante PowerShell y el SDK .NET de HDInsight.

**Requisitos previos:**

Antes de empezar este artículo, debe tener lo siguiente:

-   Un clúster de HDInsight de Azure. Para obtener más información, consulte [Introducción a HDInsight][Introducción a HDInsight] o [Aprovisionamiento de clústeres de HDInsight][Aprovisionamiento de clústeres de HDInsight].
-   Instale y configure Azure PowerShell. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][Instalación y configuración de Azure PowerShell].

## En este artículo

-   [Envío de trabajos de MapReduce mediante PowerShell][Envío de trabajos de MapReduce mediante PowerShell]
-   [Envío de trabajos de Hive mediante PowerShell][Envío de trabajos de Hive mediante PowerShell]
-   [Envío de trabajos de Sqoop mediante PowerShell][Envío de trabajos de Sqoop mediante PowerShell]
-   [Envío de trabajos de MapReduce mediante el SDK .NET de HDInsight][Envío de trabajos de MapReduce mediante el SDK .NET de HDInsight]
-   [Envío de trabajos de MapReduce de streaming de Hadoop mediante el SDK .NET de HDInsight][Envío de trabajos de MapReduce de streaming de Hadoop mediante el SDK .NET de HDInsight]
-   [Envío de trabajos de Hive mediante el SDK .NET de HDInsight][Envío de trabajos de Hive mediante el SDK .NET de HDInsight]
-   [Pasos siguientes][Pasos siguientes]

## <span id="mapreduce-powershell"></span></a> Envío de trabajos de MapReduce mediante PowerShell

Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Para obtener más información acerca del uso de PowerShell con HDInsight, consulte [Administración de HDInsight con PowerShell][Administración de HDInsight con PowerShell].

MapReduce de Hadoop es un marco de software para escribir aplicaciones que procesan enormes cantidades de datos. Los clústeres de HDInsight incluyen un archivo jar, ubicado en *\\example\\jars\\hadoop-examples.jar*, que contiene varios ejemplos de MapReduce. En la versión 3.0 de los clústeres de HDInsight, se ha cambiado el nombre de este archivo por hadoop-mapreduce-examples.jar. Uno de los ejemplos ilustra el recuento de la frecuencia de las palabras en los archivos de código fuente. En esta sesión, aprenderá a utilizar PowerShell desde una estación de trabajo para ejecutar un ejemplo de recuento de palabras. Para obtener más información acerca del desarrollo y la ejecución de trabajos de MapReduce, consulte [Uso de MapReduce con HDInsight][Uso de MapReduce con HDInsight].

**Para ejecutar el programa MapReduce de recuento de palabras mediante PowerShell**

1.  Abra **Azure PowerShell**. Para obtener instrucciones acerca de cómo abrir la ventana de la consola de Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell][Instalación y configuración de Azure PowerShell].

2.  Configure estas dos variables mediante la ejecución de los siguientes comandos de PowerShell:

        $subscriptionName = "<SubscriptionName>"   
        $clusterName = "<HDInsightClusterName>"    

    La suscripción es la que utilizó para crear el clúster de HDInsight. Y el clúster de HDInsight es el que debe utilizar para ejecutar el trabajo de MapReduce.

3.  Ejecute los siguientes comandos para crear una definición del trabajo de MapReduce:

        # Define the word count MapReduce job
        $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    Hay dos argumentos. El primero es el nombre del archivo de origen y, el segundo, la ruta de acceso del archivo de salida. Para obtener más información acerca del prefijo wasb, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][Uso del almacenamiento de blobs de Azure con HDInsight].

4.  Ejecute el siguiente comando para ejecutar el trabajo de MapReduce:

        # Submit the MapReduce job
        Select-AzureSubscription $subscriptionName
        $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 

    Además de la definición de trabajo de MapReduce, también debe proporcionar el nombre del clúster de HDInsight en el que desea ejecutar el trabajo de MapReduce.

5.  Ejecute el siguiente comando para comprobar la finalización del trabajo de MapReduce:

        # Wait for the job to complete
        Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

6.  Ejecute el siguiente comando para comprobar posibles errores al ejecutar el trabajo de MapReduce:

        # Get the job standard error output
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 

    En la captura de pantalla siguiente se muestra el resultado de una ejecución correcta. En caso contrario, aparecerán mensajes de error.

    ![HDI.GettingStarted.RunMRJob][HDI.GettingStarted.RunMRJob]

**Para recuperar los resultados del trabajo de MapReduce**

1.  Abra **Azure PowerShell**.
2.  Configure estas tres variables mediante la ejecución de los siguientes comandos de PowerShell:

        $subscriptionName = "<SubscriptionName>"       
        $storageAccountName = "<StorageAccountName>"
        $containerName = "<ContainerName>"          

    La cuenta de almacenamiento de Azure es la que especificó durante el aprovisionamiento de los clústeres de HDInsight. Esta se usa para hospedar el contenedor de blobs utilizado como sistema de archivos predeterminado del clúster de HDInsight. El nombre del contenedor de almacenamiento de blobs suele coincidir con el del clúster de HDInsight, a menos que se especifique un nombre distinto durante el aprovisionamiento del clúster.

3.  Ejecute los siguientes comandos para crear un objeto de contexto de almacenamiento de Azure:

        # Create the storage account context object
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    Select-AzureSubscription se usa para establecer la suscripción actual en caso de tener varias y no usar la suscripción predeterminada.

4.  Ejecute el siguiente comando para descargar el resultado del trabajo de MapReduce del contenedor de blobs a la estación de trabajo:

        # Get the blob content
        Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    *example/data/WordCountOutput* es la carpeta de salida especificada al ejecutar el trabajo de MapReduce. *part-r-00000* es el nombre de archivo predeterminado para el resultado del trabajo de MapReduce. El archivo se descargará a la misma estructura de carpetas de la carpeta local. Por ejemplo, en la captura de pantalla siguiente, la carpeta actual es la carpeta raíz C. El archivo se descargará en la carpeta \*C:\\example\\data\\WordCountOutput\*.

5.  Ejecute el siguiente comando para imprimir el archivo de salida del trabajo de MapReduce:

        cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    ![HDI.GettingStarted.MRJobOutput][HDI.GettingStarted.MRJobOutput]

    El trabajo de MapReduce genera un archivo denominado *part-r-00000* con las palabras y los recuentos. El script usa el comando findstr para enumerar todas las palabras que contienen "there".

> [WACOM.NOTE] Si se abre en el Bloc de notas ./example/data/WordCountOutput/part-r-00000, resultado de varias líneas de un trabajo de MapReduce, observará que los saltos de línea no se representan correctamente. Se espera que esto sea así.

## <span id="hive-powershell"></span></a> Envío de trabajos de Hive mediante PowerShell

Apache [hdinsight-use-hive][hdinsight-use-hive] ofrece el modo de ejecutar un trabajo de MapReduce mediante un lenguaje de scripting de tipo SQL, llamado *HiveQL*, que se puede aplicar al resumen, la consulta y el análisis de grandes volúmenes de datos.

Los clústeres de HDInsight incluyen un ejemplo de tabla de Hive con nombre *hivesampletable*. En esta sesión, utilizará PowerShell para ejecutar un trabajo de Hive con el fin de obtener un listado de algunos datos de la tabla de Hive.

**Para ejecutar un trabajo de Hive mediante PowerShell**

1.  Abra **Azure PowerShell**. Para obtener instrucciones acerca de cómo abrir la ventana de la consola de Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell][Instalación y configuración de Azure PowerShell].

2.  Establezca las dos primeras variables en los comandos siguientes y, a continuación, ejecútelos:

        $subscriptionName = "<SubscriptionName>"   
        $clusterName = "<HDInsightClusterName>"             
        $querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"

    $querystring es la consulta de HiveQL.

3.  Ejecute los siguientes comandos para seleccionar la suscripción de Azure y el clúster para ejecutar el trabajo de Hive:

        Select-AzureSubscription -SubscriptionName $subscriptionName

4.  Envío del trabajo de Hive:

        Use-AzureHDInsightCluster $clusterName
        Invoke-Hive -Query $queryString

    Puede utilizar el modificador -File para especificar un archivo de scripts de HiveQL en HDFS.

Para obtener más información acerca de Hive, consulte [Uso de Hive con HDInsight][Uso de Hive con HDInsight].

## <span id="sqoop-powershell"></span></a>Envío de trabajos de Sqoop mediante PowerShell

Consulte [Uso de Sqoop con HDInsight][Uso de Sqoop con HDInsight].

## <span id="mapreduce-sdk"></span></a> Envío de trabajos de MapReduce mediante el SDK .NET de HDInsight

El SDK .NET de HDInsight proporciona bibliotecas de cliente .NET que facilitan el trabajo con los clústeres de HDInsight en .NET. Los clústeres de HDInsight incluyen un archivo jar, ubicado en *\\example\\jars\\hadoop-examples.jar*, que contiene varios ejemplos de MapReduce. Uno de los ejemplos ilustra el recuento de la frecuencia de las palabras en los archivos de código fuente. En esta sesión, aprenderá a crear una aplicación .NET para ejecutar un ejemplo de recuento de palabras. Para obtener más información acerca del desarrollo y la ejecución de trabajos de MapReduce, consulte [Uso de MapReduce con HDInsight][Uso de MapReduce con HDInsight].

Los siguientes procedimientos son necesarios para aprovisionar un clúster de HDInsight con el SDK:

-   Instalar el SDK .NET de HDInsight
-   Creación de una aplicación de consola
-   Ejecución de la aplicación

**Para instalar el SDK .NET de HDInsight:**
 puede instalar la última compilación publicada del SDK en [NuGet][NuGet]. Las instrucciones se mostrarán en el siguiente procedimiento.

**Para crear una aplicación de consola de Visual Studio**

1.  Abra Visual Studio 2012.

2.  En el menú Archivo, haga clic en **Nuevo** y, a continuación, haga clic en **Proyecto**.

3.  En Nuevo proyecto, escriba o seleccione los siguientes valores:

    | Propiedad | Valor                         |
    |-----------|-------------------------------|
    | Category  | Plantillas/Visual C#/Windows |
    | Plantilla | Aplicación de consola         |
    | Nombre    | SubmitMapReduceJob            |

4.  Haga clic en **Aceptar** para crear el proyecto.

5.  En el menú **Herramientas**, haga clic en **Library Package Manager** y, a continuación, en **Package Manager Console**.

6.  Ejecute los siguientes comandos en la consola para instalar los paquetes.

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

    Este comando agrega las bibliotecas y referencias .NET a los paquetes para el proyecto de Visual Studio actual. La versión debe ser la 0.11.0.1 o posterior.

7.  En el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo.

8.  Agregue la siguiente instrucción en la parte superior del archivo:

        using System.IO;
        using System.Threading;
        using System.Security.Cryptography.X509Certificates;

        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

        using Microsoft.WindowsAzure.Management.HDInsight;
        using Microsoft.Hadoop.Client;

9.  Agregue la siguiente definición de función a la clase. Esta función se utiliza para esperar a que se haya completado el trabajo de Hadoop.

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

        // Set the variables
        string subscriptionID = "<Azure subscription ID>";
        string certFriendlyName = "<certificate friendly name>";

        string clusterName = "<HDInsight cluster name>";

        string storageAccountName = "<Azure storage account name>";
        string storageAccountKey = "<Azure storage account key>";
        string containerName = "<Blob container name>";

    Estas son todas las variables que necesita configurar para el programa. Puede obtener el nombre de la Suscripción de Azure en el [Portal de administración de Azure][Portal de administración de Azure].

    Para obtener información acerca del certificado, consulte [Crear y cargar un certificado de administración para Azure][Crear y cargar un certificado de administración para Azure]. Una forma sencilla de configurar el certificado es ejecutar los cmdlets de PowerShell *Get-AzurePublishSettingsFile* y *Import-AzurePublishSettingsFile*. Estos crearán y cargarán el certificado de administración automáticamente. Una vez ejecutados los cmdlets de PowerShell, puede abrir *certmgr.msc* desde la estación de trabajo y encontrar el certificado al expandir *Personal/Certificates*. El certificado creado mediante los cmdlets de PowerShell incluye *Azure Tools* en los campos *Issued To* e *Issued By*.

    El nombre de la cuenta de almacenamiento de Azure es la cuenta que especifica al aprovisionar el clúster de HDInsight. El nombre del contenedor predeterminado es el mismo que el del clúster de HDInsight.

11. En la función Main(), anexe el siguiente código para definir el trabajo de MapReduce:

        // Define the MapReduce job
        MapReduceJobCreateParameters mrJobDefinition = new MapReduceJobCreateParameters()
        {
            JarFile = "wasb:///example/jars/hadoop-examples.jar",
            ClassName = "wordcount"
        };

        mrJobDefinition.Arguments.Add("wasb:///example/data/gutenberg/davinci.txt");
        mrJobDefinition.Arguments.Add("wasb:///example/data/WordCountOutput");

    Hay dos argumentos. El primero es el nombre del archivo de origen y, el segundo, la ruta de acceso del archivo de salida. Para obtener más información acerca del prefijo wasb, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][Uso del almacenamiento de blobs de Azure con HDInsight].

12. En la función Main(), anexe el siguiente código para crear un objeto JobSubmissionCertificateCredential:

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

13. En la función Main(), anexe el siguiente código para ejecutar el trabajo y esperar a que se haya completado:

        // Create a hadoop client to connect to HDInsight
        var jobClient = JobSubmissionClientFactory.Connect(creds);

        // Run the MapReduce job
        JobCreationResults mrJobResults = jobClient.CreateMapReduceJob(mrJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(mrJobResults, jobClient);

14. En la función Main(), anexe el siguiente código para imprimir el resultado del trabajo de MapReduce:

        // Print the MapReduce job output
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

    La carpeta de destino se especifica al definir el trabajo de MapReduce. El nombre del archivo predeterminado es *part-r-00000*.

**Para ejecutar la aplicación**

Mientras la aplicación está abierta en Visual Studio, presione **F5** para ejecutarla. Una ventana de consola se abrirá y mostrará el estado y el resultado de la aplicación.

## <span id="streaming-sdk"></span></a> Envío de trabajos de streaming de Hadoop mediante el SDK .NET de HDInsight

Los clústeres de HDInsight cuentan con un programa de streaming de Hadoop de recuento de palabras desarrollado en C#. El programa del asignador es */example/apps/cat.exe*, y el de MapReduce es */example/apps/wc.exe*. En esta sesión, aprenderá a crear una aplicación .NET para ejecutar un ejemplo de recuento de palabras.

Para obtener detalles sobre la creación de una aplicación .Net para enviar trabajos de MapReduce, consulte [Envío de trabajos de MapReduce mediante el SDK .NET de HDInsight][Envío de trabajos de MapReduce mediante el SDK .NET de HDInsight].

Para obtener más información sobre el desarrollo e implementación de trabajos de streaming de Hadoop, consulte [Desarrollo de programas de streaming de Hadoop C# para HDInsight][Desarrollo de programas de streaming de Hadoop C# para HDInsight].

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    using System.IO;
    using System.Threading;
    using System.Security.Cryptography.X509Certificates;

    using Microsoft.WindowsAzure.Management.HDInsight;
    using Microsoft.Hadoop.Client;

    namespace SubmitStreamingJob
    {
        class Program
        {
            static void Main(string[] args)
            {

                // Set the variables
                string subscriptionID = "<Azure subscription ID>";
                string certFriendlyName = "<certificate friendly name>";
        
                string clusterName = "<HDInsight cluster name>";
                string statusFolderName = @"/tutorials/wordcountstreaming/status";

                // Define the Hadoop streaming MapReduce job
                StreamingMapReduceJobCreateParameters myJobDefinition = new StreamingMapReduceJobCreateParameters()
                {
                    JobName = "my word counting job",
                    StatusFolder = statusFolderName,
                    Input = "/example/data/gutenberg/davinci.txt",
                    Output = "/tutorials/wordcountstreaming/output",
                    Reducer = "wc.exe",
                    Mapper = "cat.exe"
                };

                myJobDefinition.Files.Add("/example/apps/wc.exe");
                myJobDefinition.Files.Add("/example/apps/cat.exe");

                // Get the certificate object from certificate store using the friendly name to identify it
                X509Store store = new X509Store();
                store.Open(OpenFlags.ReadOnly);
                X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);

                JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

                // Create a hadoop client to connect to HDInsight
                var jobClient = JobSubmissionClientFactory.Connect(creds);

                // Run the MapReduce job
                Console.WriteLine("----- Submit the Hadoop streaming job ...");
                JobCreationResults mrJobResults = jobClient.CreateStreamingJob(myJobDefinition);

                // Wait for the job to complete
                Console.WriteLine("----- Wait for the Hadoop streaming job to complete ...");
                WaitForJobCompletion(mrJobResults, jobClient);

                // Display the error log
                Console.WriteLine("----- The hadoop streaming job error log.");
                using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
                {
                    var reader = new StreamReader(stream);
                    Console.WriteLine(reader.ReadToEnd());
                }

                // Display the output log
                Console.WriteLine("----- The hadoop streaming job output log.");
                using (Stream stream = jobClient.GetJobOutput(mrJobResults.JobId))
                {
                    var reader = new StreamReader(stream);
                    Console.WriteLine(reader.ReadToEnd());
                }

                Console.WriteLine("----- Press ENTER to continue.");
                Console.ReadLine();
            }

            private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
            {
                JobDetails jobInProgress = client.GetJob(jobResults.JobId);
                while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
                {
                    jobInProgress = client.GetJob(jobInProgress.JobId);
                    Thread.Sleep(TimeSpan.FromSeconds(10));
                }
            }
        }
    }

## <span id="hive-sdk"></span></a> Envío de trabajos de Hive mediante el SDK .NET de HDInsight

Los clústeres de HDInsight incluyen un ejemplo de tabla de Hive con nombre *hivesampletable*. En esta sesión, creará una aplicación .NET para ejecutar un trabajo de Hive con el fin de obtener un listado de las tablas de Hive creadas en el clúster de HDInsight. Para obtener más información acerca del uso de Hive, consulte [Uso de Hive con HDInsight][Uso de Hive con HDInsight].

Los siguientes procedimientos son necesarios para aprovisionar un clúster de HDInsight con el SDK:

-   Instalar el SDK .NET de HDInsight
-   Creación de una aplicación de consola
-   Ejecución de la aplicación

**Para instalar el SDK .NET de HDInsight:**
 puede instalar la última compilación publicada del SDK en [NuGet][NuGet]. Las instrucciones se mostrarán en el siguiente procedimiento.

**Para crear una aplicación de consola de Visual Studio**

1.  Abra Visual Studio 2012.

2.  En el menú Archivo, haga clic en **Nuevo** y, a continuación, haga clic en **Proyecto**.

3.  En Nuevo proyecto, escriba o seleccione los siguientes valores:

    | Propiedad | Valor                         |
    |-----------|-------------------------------|
    | Category  | Plantillas/Visual C#/Windows |
    | Plantilla | Aplicación de consola         |
    | Nombre    | EnviarTrabajoDeHive           |

4.  Haga clic en **Aceptar** para crear el proyecto.

5.  En el menú **Herramientas**, haga clic en **Library Package Manager** y, a continuación, en **Package Manager Console**.

6.  Ejecute los siguientes comandos en la consola para instalar los paquetes.

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

    Este comando agrega las bibliotecas y referencias .NET a los paquetes para el proyecto de Visual Studio actual.

7.  En el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo.

8.  Agregue la siguiente instrucción en la parte superior del archivo:

        using System.IO;
        using System.Threading;
        using System.Security.Cryptography.X509Certificates;

        using Microsoft.WindowsAzure.Management.HDInsight;
        using Microsoft.Hadoop.Client;

9.  Agregue la siguiente definición de función a la clase. Esta función se utiliza para esperar a que se haya completado el trabajo de Hadoop.

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

        // Set the variables
        string subscriptionID = "<Azure subscription ID>";
        string clusterName = "<HDInsight cluster name>";
        string certFriendlyName = "<certificate friendly name>";        

    Estas son todas las variables que necesita configurar para el programa. Puede obtener el identificador de la suscripción de Azure desde el administrador del sistema.

    Para obtener información acerca del certificado, consulte [Crear y cargar un certificado de administración para Azure][Crear y cargar un certificado de administración para Azure]. Una forma sencilla de configurar el certificado es ejecutar los cmdlets de PowerShell *Get-AzurePublishSettingsFile* y *Import-AzurePublishSettingsFile*. Estos crearán y cargarán el certificado de administración automáticamente. Una vez ejecutados los cmdlets de PowerShell, puede abrir *certmgr.msc* desde la estación de trabajo y encontrar el certificado al expandir *Personal/Certificates*. El certificado creado mediante los cmdlets de PowerShell incluye *Azure Tools* en los campos *Issued To* e *Issued By*.

11. En la función Main(), anexe el siguiente código para definir el trabajo de Hive:

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            Query = "show tables;"
        };

    Puede utilizar el parámetro File para especificar un archivo de scripts de HiveQL en HDFS. Por ejemplo:

        // define the Hive job
        HiveJobCreateParameters hiveJobDefinition = new HiveJobCreateParameters()
        {
            JobName = "show tables job",
            StatusFolder = "/ShowTableStatusFolder",
            File = "/user/admin/showtables.hql"
        };

12. En la función Main(), anexe el siguiente código para crear un objeto JobSubmissionCertificateCredential:

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
        JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);

13. En la función Main(), anexe el siguiente código para ejecutar el trabajo y esperar a que se haya completado:

        // Submit the Hive job
        var jobClient = JobSubmissionClientFactory.Connect(creds);
        JobCreationResults jobResults = jobClient.CreateHiveJob(hiveJobDefinition);

        // Wait for the job to complete
        WaitForJobCompletion(jobResults, jobClient);

14. En la función Main(), anexe el siguiente código para imprimir el resultado del trabajo de Hive:

        // Print the Hive job output
        System.IO.Stream stream = jobClient.GetJobOutput(jobResults.JobId);

        StreamReader reader = new StreamReader(stream);
        Console.WriteLine(reader.ReadToEnd());

        Console.WriteLine("Press ENTER to continue.");
        Console.ReadLine();

**Para ejecutar la aplicación**

Mientras la aplicación está abierta en Visual Studio, presione **F5** para ejecutarla. Una ventana de consola se abrirá y mostrará el estado de la aplicación. El resultado debe ser:

    hivesampletable

## <span id="nextsteps"></span></a>Pasos siguientes

En este artículo, ha aprendido a aprovisionar un clúster de HDInsight de varias formas. Para obtener más información, consulte los artículos siguientes:

-   [Introducción a HDInsight de Azure][Introducción a HDInsight]
-   [Aprovisionamiento de clústeres de HDInsight][Aprovisionamiento de clústeres de HDInsight]
-   [Administración de HDInsight con PowerShell][Administración de HDInsight con PowerShell]
-   [Documentación de referencia de los cmdlets de HDInsight][Documentación de referencia de los cmdlets de HDInsight]
-   [Uso de Hive con HDInsight][Uso de Hive con HDInsight]
-   [Uso de Pig con HDInsight][Uso de Pig con HDInsight]

  [Introducción a HDInsight]: ../hdinsight-get-started/
  [Aprovisionamiento de clústeres de HDInsight]: ../hdinsight-provision-clusters/
  [Instalación y configuración de Azure PowerShell]: ../install-configure-powershell/
  [Envío de trabajos de MapReduce mediante PowerShell]: #mapreduce-powershell
  [Envío de trabajos de Hive mediante PowerShell]: #hive-powershell
  [Envío de trabajos de Sqoop mediante PowerShell]: #sqoop-powershell
  [Envío de trabajos de MapReduce mediante el SDK .NET de HDInsight]: #mapreduce-sdk
  [Envío de trabajos de MapReduce de streaming de Hadoop mediante el SDK .NET de HDInsight]: #streaming-sdk
  [Envío de trabajos de Hive mediante el SDK .NET de HDInsight]: #hive-sdk
  [Pasos siguientes]: #nextsteps
  [Administración de HDInsight con PowerShell]: ../hdinsight-administer-use-powershell/
  [Uso de MapReduce con HDInsight]: ../hdinsight-use-mapreduce/
  [Uso del almacenamiento de blobs de Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [HDI.GettingStarted.RunMRJob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png
  [HDI.GettingStarted.MRJobOutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png
  [hdinsight-use-hive]: http://hive.apache.org/
  [Uso de Hive con HDInsight]: ../hdinsight-use-hive/
  [Uso de Sqoop con HDInsight]: ../hdinsight-use-sqoop/
  [NuGet]: http://nuget.codeplex.com/wikipage?title=Getting%20Started
  [Portal de administración de Azure]: http://manage.windowsazure.com/
  [Crear y cargar un certificado de administración para Azure]: http://msdn.microsoft.com/es-es/library/windowsazure/gg551722.aspx
  [Desarrollo de programas de streaming de Hadoop C# para HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Documentación de referencia de los cmdlets de HDInsight]: http://msdn.microsoft.com/es-es/library/windowsazure/dn479228.aspx
  [Uso de Pig con HDInsight]: ../hdinsight-use-pig/
