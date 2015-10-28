<properties
	pageTitle="Envío de trabajos de Hadoop en HDInsight | Microsoft Azure"
	description="Aprenda a enviar trabajos de Hadoop a HDInsight Hadoop de Azure."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/02/2015"
	ms.author="jgao"/>

# Envío de trabajos de Hadoop en HDInsight

Aprenda a usar Azure PowerShell para enviar trabajos de MapReduce y Hive, y a usar el SDK de .NET de HDInsight para enviar trabajos de Hive, streaming de Hadoop y MapReduce.

> [AZURE.NOTE]Los pasos de este artículo deben realizarse desde un cliente de Windows. Para obtener información sobre el uso de un cliente Linux, OS X o Unix para trabajar con MapReduce, Hive o Pig en HDInsight, consulte los siguientes artículos y seleccione los vínculos **SSH** o **Curl** de cada uno:
>
> - [Uso de Hive con HDInsight](hdinsight-use-hive.md)
> - [Uso de Pig con HDInsight](hdinsight-use-pig.md)
> - [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

##Requisitos previos

Antes de empezar este artículo, debe tener lo siguiente:

* **Un clúster de HDInsight de Azure**. Para obtener más información, consulte [Introducción a HDInsight][hdinsight-get-started] o [Creación de clústeres de Hadoop en HDInsight][hdinsight-provision].
- **Una estación de trabajo con Azure PowerShell**. Consulte [Install and use Azure PowerShell (Instalación y uso de Azure PowerShell)](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



##Envío de trabajos de MapReduce mediante Azure PowerShell
Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Para obtener más información sobre el uso de Azure PowerShell con HDInsight, consulte [Administración de HDInsight mediante PowerShell][hdinsight-admin-powershell].

MapReduce de Hadoop es un marco de software para escribir aplicaciones que procesan enormes cantidades de datos. Los clústeres de HDInsight incluyen un archivo JAR (ubicado en *\\example\\jars\\hadoop-mapreduce-examples.jar*) que contiene varios ejemplos de MapReduce.

Uno de los ejemplos ilustra el recuento de la frecuencia de las palabras en los archivos de código fuente. En esta sesión, aprenderá a utilizar Azure PowerShell desde una estación de trabajo para ejecutar una muestra de recuento de palabras. Para obtener más información sobre el desarrollo y la ejecución de trabajos de MapReduce, consulte [Uso de MapReduce con HDInsight][hdinsight-use-mapreduce].

**Para ejecutar el programa MapReduce de recuento de palabras mediante Azure PowerShell**

1.	Abra **Azure PowerShell**. Para obtener instrucciones sobre cómo abrir la ventana de la consola de Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].

3. Configure las variables siguientes mediante la ejecución de los siguientes comandos de Azure PowerShell:

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<HDInsightClusterName>"

	El nombre de suscripción es el que usó para crear el clúster de HDInsight. El clúster de HDInsight es el que quiere usar para ejecutar el trabajo de MapReduce.

5. Ejecute los siguientes comandos para crear una definición del trabajo de MapReduce:

		# Define the word count MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	Hay dos argumentos. El primero es el nombre del archivo de origen y, el segundo, la ruta de acceso del archivo de salida. Para obtener más información sobre el prefijo wasb://, consulte [Uso del almacenamiento de blobs de Azure con HDInsight][hdinsight-storage].

6. Ejecute el siguiente comando para ejecutar el trabajo de MapReduce:

		# Submit the MapReduce job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition

	Además de la definición del trabajo de MapReduce, también debe facilitar el nombre del clúster de HDInsight en el que desea ejecutar el trabajo de MapReduce.

7. Ejecute el siguiente comando para comprobar la finalización del trabajo de MapReduce:

		# Wait for the job to complete
		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600


8. Ejecute el siguiente comando para comprobar posibles errores al ejecutar el trabajo de MapReduce:

		# Get the job standard error output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

	En la captura de pantalla siguiente se muestra el resultado de una ejecución correcta. En caso contrario, aparecerán mensajes de error.

	![HDI.GettingStarted.RunMRJob][image-hdi-gettingstarted-runmrjob]


**Para recuperar los resultados del trabajo de MapReduce**

1. Abra **Azure PowerShell**.
2. Configure las variables siguientes mediante la ejecución de los siguientes comandos de Azure PowerShell:

		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

	El nombre de la cuenta de almacenamiento es la cuenta de almacenamiento de Azure que especificó durante la creación del clúster de HDInsight. Se usa para hospedar el contenedor de blobs que se usa como sistema de archivos predeterminado del clústeres de HDInsight. El nombre del contenedor suele coincidir con el del clúster de HDInsight, a menos que se especifique un nombre distinto durante la creación del clúster.

3. Ejecute los siguientes comandos para crear un objeto de contexto de almacenamiento de blobs de Azure:

		# Create the storage account context object
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Se usa **Select-AzureSubscription** para establecer la suscripción actual si tiene varias y no usa la suscripción predeterminada.

4. Ejecute el siguiente comando para descargar el resultado del trabajo de MapReduce del contenedor de blobs a la estación de trabajo:

		# Get the blob content
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	La carpeta *example/data/WordCountOutput* es la carpeta de salida especificada al ejecutar el trabajo de MapReduce. *part-r-00000* es el nombre de archivo predeterminado para el resultado del trabajo de MapReduce. El archivo se descargará en la misma estructura de carpetas de la carpeta local. Por ejemplo, en la captura de pantalla siguiente, la carpeta actual es la carpeta raíz C:. El archivo se descargará en:

**C:\\example\\data\\WordCountOutput*

5. Ejecute el siguiente comando para imprimir el archivo de salida del trabajo de MapReduce:

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	![HDI.GettingStarted.MRJobOutput][image-hdi-gettingstarted-mrjoboutput]

	El trabajo de MapReduce genera un archivo denominado *part-r-00000*, que contiene las palabras y los recuentos. El script usa el comando **findstr** para enumerar todas las palabras que contienen "there".


> [AZURE.NOTE]Si abre en el Bloc de notas ./example/data/WordCountOutput/part-r-00000 (un resultado de varias líneas de un trabajo de MapReduce), observará que los saltos de línea no se representan correctamente. Se espera que esto sea así.









































































































































##Envío de trabajos de Hive mediante Azure PowerShell
[Apache Hive][apache-hive] ofrece un modo de ejecutar un trabajo de MapReduce mediante un lenguaje de scripting de tipo SQL, llamado *HiveQL*, que se puede aplicar para resumir, consultar y analizar grandes volúmenes de datos.

Los clústeres de HDInsight incluyen un ejemplo de tabla de Hive con nombre *hivesampletable*. En esta sesión, usará Azure PowerShell para ejecutar un trabajo de Hive con el fin de obtener un listado de algunos datos de la tabla de Hive.

**Para ejecutar un trabajo de Hive mediante Azure PowerShell**

1.	Abra **Azure PowerShell**. Para obtener instrucciones sobre cómo abrir la ventana de la consola de Azure PowerShell, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure].

2. Establezca las dos primeras variables en los comandos siguientes y, a continuación, ejecútelos:

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<HDInsightClusterName>"
		$querystring = "SELECT * FROM hivesampletable WHERE Country='United Kingdom';"

	$querystring es la consulta de HiveQL.

3. Ejecute el comando siguiente para seleccionar la suscripción de Azure y el clúster para ejecutar el trabajo de Hive:

		Select-AzureSubscription -SubscriptionName $subscriptionName

4. Ejecute los comandos siguientes para enviar un trabajo de Hive:

		Use-AzureHDInsightCluster $clusterName
		Invoke-Hive -Query $queryString

	Puede utilizar el modificador **-File** para especificar un archivo de script de HiveQL en el sistema de archivos distribuido de Hadoop (HDFS).

Para obtener más información sobre Hive, consulte [Uso de Hive con HDInsight][hdinsight-use-hive].


## Envío de trabajos de Hive mediante Visual Studio

Consulte [Introducción al uso de herramientas de Hadoop de HDInsight para Visual Studio][hdinsight-visual-studio-tools].

##Envío de trabajos de Sqoop mediante Azure PowerShell

Consulte [Uso de Sqoop con HDInsight][hdinsight-use-sqoop].

##Envío de trabajos de MapReduce mediante el SDK de .NET de HDInsight
El SDK .NET de HDInsight ofrece bibliotecas de cliente .NET que facilitan el trabajo con los clústeres de HDInsight de .NET. Los clústeres de HDInsight incluyen un archivo JAR (ubicado en *\\example\\jars\\hadoop-mapreduce-examples.jar*) que contiene varios ejemplos de MapReduce. Uno de los ejemplos ilustra el recuento de la frecuencia de las palabras en los archivos de código fuente. En esta sesión, aprenderá a crear una aplicación .NET para ejecutar un ejemplo de recuento de palabras. Para obtener más información sobre el desarrollo y la ejecución de trabajos de MapReduce, consulte [Uso de MapReduce con HDInsight][hdinsight-use-mapreduce].

**Para enviar el trabajo WordCount de MapReduce**

1. Cree una aplicación de consola en C# mediante Visual Studio.
2. En la Consola del administrador de paquetes Nuget, ejecute el siguiente comando.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. Use las siguientes instrucciones using en el archivo Program.cs:

		using System;
		using System.Collections.Generic;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;

3. En la función Main(), agregue el código siguiente.

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
	    HDInsightJobManagementClient _hdiJobManagementClient;

	    List<string> arguments = new List<string> { { "wasb:///example/data/gutenberg/davinci.txt" }, { "wasb:///example/data/WordCountOutput" } };
	
	    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
	    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
	
	    var parameters = new MapReduceJobSubmissionParameters
	    {
	        UserName = ExistingClusterUsername,
	        JarFile = "wasb:///example/jars/hadoop-mapreduce-examples.jar",
	        JarClass = "wordcount",
	        Arguments = ConvertArgsToString(arguments)
	    };
	
	    System.Console.WriteLine("Submitting the MapReduce job to the cluster...");
	    var response = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(parameters);
	    System.Console.WriteLine("Validating that the response is as expected...");
	    System.Console.WriteLine("Response status code is " + response.StatusCode);
	    System.Console.WriteLine("Validating the response object...");
	    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
	    Console.WriteLine("Press ENTER to continue ...");
	    Console.ReadLine();

4. Agregue la función auxiliar siguiente a la clase.

        private static string ConvertArgsToString(List<string> args)
        {
            if (args.Count == 0)
            {
                return null;
            }

            return string.Join("&arg=", args.ToArray());
        }

5. Presione **F5** para ejecutar la aplicación.










##Envío de trabajos de streaming de Hadoop mediante el SDK .NET de HDInsight
Los clústeres de HDInsight cuentan con un programa de streaming de Hadoop de recuento de palabras desarrollado en C#. El programa mapeador es */example/apps/cat.exe* y el de reducción es */example/apps/wc.exe*. En esta sesión, aprenderá a crear una aplicación .NET para ejecutar la muestra de recuento de palabras.

Para obtener detalles sobre la creación de una aplicación en .NET para enviar trabajos de MapReduce, consulte [Envío de trabajos de MapReduce mediante el SDK de .NET de HDInsight](#mapreduce-sdk).

Para obtener más información sobre el desarrollo e implementación de trabajos de streaming de Hadoop, consulte [Desarrollo de programas de streaming de Hadoop C# para HDInsight][hdinsight-develop-streaming-jobs].

El siguiente procedimiento solo funciona en los clústeres de HDInsight en Windows. El streaming en C# no se admite aún en clústeres de Linux. Sin embargo, puede usar el programa de .NET para enviar trabajo de transmisión escrito en otros lenguajes de programación que son compatibles con clústeres de Linux. Por ejemplo, Python. Para obtener un ejemplo de streaming para Python, consulte [Desarrollo de programas de streaming de Python para HDInsight](hdinsight-hadoop-streaming-python.md).

**Para enviar el trabajo WordCount de MapReduce**

1. En la Consola del administrador de paquetes de Visual Studio, ejecute el siguiente comando de Nuget para importar el paquete.

		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

2. Use las siguientes instrucciones using en el archivo Program.cs:

		using System;
		using System.Collections.Generic;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;

3. En la función Main(), agregue el código siguiente.

		var ExistingClusterName = "<HDInsightClusterName>";
		var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
		var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";

        List<string> arguments = new List<string> { { "/example/apps/cat.exe" }, { "/example/apps/wc.exe" } };

        HDInsightJobManagementClient _hdiJobManagementClient;
        var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
        _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

        var parameters = new MapReduceStreamingJobSubmissionParameters
        {
            UserName = ExistingClusterUsername,
            File = ConvertArgsToString(arguments),
            Mapper = "cat.exe",
            Reducer = "wc.exe",
            Input = "/example/data/gutenberg/davinci.txt",
            Output = "/tutorials/wordcountstreaming/output"
        };

        System.Console.WriteLine("Submitting the MapReduce job to the cluster...");
        var response = _hdiJobManagementClient.JobManagement.SubmitMapReduceStreamingJob(parameters);
        System.Console.WriteLine("Validating that the response is as expected...");
        System.Console.WriteLine("Response status code is " + response.StatusCode);
        System.Console.WriteLine("Validating the response object...");
        System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
        Console.WriteLine("Press ENTER to continue ...");
        Console.ReadLine();

4. Agregue una función auxiliar a la clase.

        private static string ConvertArgsToString(List<string> args)
        {
            if (args.Count == 0)
            {
                return null;
            }

            return string.Join("&arg=", args.ToArray());
        }

5. Presione **F5** para ejecutar la aplicación.

##Envío de trabajos de Hive mediante el SDK .NET de HDInsight
Los clústeres de HDInsight incluyen una muestra de tabla de Hive con el nombre *hivesampletable*. En esta sesión, creará una aplicación .NET para ejecutar un trabajo de Hive con el fin de obtener un listado de las tablas de Hive creadas en un clúster de HDInsight. Para obtener más información sobre el uso de Hive, consulte [Uso de Hive con HDInsight][hdinsight-use-hive].

Los siguientes procedimientos son necesarios para crear un clúster de HDInsight mediante el SDK:

- Instalación del SDK de .NET de HDInsight
- Creación de una aplicación de consola
- Ejecución de la aplicación


**Para instalar el SDK .NET de HDInsight** puede instalar la última compilación publicada del SDK en [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). Las instrucciones se mostrarán en el siguiente procedimiento.

**Para crear una aplicación de consola de Visual Studio**

1. Abra Visual Studio 2013 o 2015

2. Cree un proyecto con la configuración siguiente:

	|Propiedad|Valor|
	|--------|-----|
	|Plantilla|Plantillas/Visual C#/Windows/Aplicación de consola|
	|Nombre|EnviarTrabajoDeHive|

3. En el menú **Herramientas**, haga clic en **Administrador de paquetes NuGet** y, a continuación, en la **Consola del administrador de paquetes**.
4. Ejecute el siguiente comando en la consola para instalar los paquetes:

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

	Estos comandos agregan las bibliotecas y referencias .NET al proyecto de Visual Studio actual.

5. En el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo, pegue el siguiente código y especifique valores para las variables:

		using System.Collections.Generic;
		using System.Linq;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Hyak.Common;
		
		namespace SubmitHiveJob
		{
		    class Program
		    {
		        private static HDInsightJobManagementClient _hdiJobManagementClient;
		
		        private const string ExistingClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
		
		        private const string ExistingClusterUsername = "<HDINSIGHT HTTP USER NAME>";  //The default name is admin.
		        private const string ExistingClusterPassword = "<HDINSIGHT HTTP USER PASSWORD>";
		
		        private static void Main(string[] args)
		        {
		
		            var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
		            _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
		
		            SubmitHiveJob();
		        }
		
		        private static void SubmitHiveJob()
		        {
		            Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "ravi" }, { "hive.exec.reducers.max", "1" } };
		            List<string> args = new List<string> { { "argA" }, { "argB" } };
		            var parameters = new HiveJobSubmissionParameters
		            {
		                UserName = ExistingClusterUsername,
		                Query = "SHOW TABLES",
		                Defines = ConvertDefinesToString(defines),
		                Arguments = ConvertArgsToString(args)
		            };
		
		            System.Console.WriteLine("Submitting the Hive job to the cluster...");
		            var response = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
		            System.Console.WriteLine("Validating that the response is as expected...");
		            System.Console.WriteLine("Response status code is " + response.StatusCode);
		            System.Console.WriteLine("Validating the response object...");
		            System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
		            System.Console.WriteLine("Press ENTER to continue ...");
		            System.Console.ReadLine();
		        }
		
		        private static string ConvertDefinesToString(Dictionary<string, string> defines)
		        {
		            if (defines.Count == 0)
		            {
		                return null;
		            }
		
		            return string.Join("&define=", defines.Select(x => x.Key + "%3D" + x.Value).ToArray());
		        }
		        private static string ConvertArgsToString(List<string> args)
		        {
		            if (args.Count == 0)
		            {
		                return null;
		            }
		
		            return string.Join("&arg=", args.ToArray());
		        }
		    }
		}

6. Presione **F5** para ejecutar la aplicación.

##Enviar consultas de Hive mediante las herramientas de HDInsight para Visual Studio

Mediante el uso de las herramientas de HDInsight para Visual Studio, puede ejecutar las consultas de Hive y los scripts de Pig. Consulte [Introducción al uso de herramientas de Hadoop de Visual Studio para HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md).


##Pasos siguientes
En este artículo, ha aprendido varias maneras de crear un clúster de HDInsight. Para obtener más información, consulte los artículos siguientes:

* [Introducción a HDInsight de Azure][hdinsight-get-started]
* Consulte [Creación de clústeres de Hadoop en HDInsight][hdinsight-provision].
* [Administración de HDInsight mediante PowerShell][hdinsight-admin-powershell]
* [Documentación de referencia de los cmdlets de HDInsight][hdinsight-powershell-reference]
* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Uso de Pig con HDInsight][hdinsight-use-pig]


[azure-certificate]: http://msdn.microsoft.com/library/windowsazure/gg551722.aspx
[azure-management-portal]: https://portal.azure.com/

[hdinsight-visual-studio-tools]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-submit-hadoop-jobs-programmatically/HDI.GettingStarted.MRJobOutput.png

[apache-hive]: http://hive.apache.org/

<!---HONumber=Oct15_HO3-->