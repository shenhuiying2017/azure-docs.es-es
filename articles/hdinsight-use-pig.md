<properties 
	pageTitle="Uso de Pig con Hadoop en HDInsight | Azure" 
	description="Aprenda a usar Pig con HDInsight. Escriba instrucciones de Pig Latin para analizar el archivo de registro de una aplicación y ejecute consultas en los datos con el fin de generar resultados para el análisis." 
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



# Uso de Pig con Hadoop en HDInsight

En este tutorial aprenderá a ejecutar trabajos de [Apache Pig][apachepig-home] en HDInsight para analizar archivos de datos de gran tamaño. Pig proporciona un lenguaje de scripting para ejecutar trabajos de  *MapReduce* como alternativa a escribir código Java. El lenguaje de scripting de Pig se denomina  *Pig Latin*.

**Requisitos previos**

* Debe aprovisionar un clúster de HDInsight de Azure. Para obtener instrucciones, consulte [Introducción a HDInsight de Azure][hdinsight-get-started] o [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision]. Necesitará el nombre del clúster para completar el tutorial.

* Debe tener instalado y configurado Azure PowerShell en su estación de trabajo. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][powershell-install-configure]. 

**Tiempo estimado para completar el tutorial:** 30 minutos

## En este artículo

* [¿Por qué usar Pig?](#usage)
* [¿Qué hago en este tutorial?](#what)
* [Identificación de datos para analizar](#data)
* [Descripción de Pig Latin](#understand)
* [Envío de trabajos de Pig mediante PowerShell](#powershell)
* [Envío de trabajos de Pig mediante el SDK .NET de HDInsight](#sdk)
* [Pasos siguientes](#nextsteps)
 
## <a id="usage"></a>¿Por qué usar Pig?
Trabajar con datos de gran tamaño es difícil si se usan bases de datos relacionales y paquetes de estadísticas/visualización. Debido a las grandes cantidades de datos y el cálculo de los mismos, a menudo es necesario tener software que se ejecute en paralelo en decenas, cientos o incluso miles de servidores para calcular estos datos en un plazo razonable. Hadoop proporciona un marco de  *MapReduce* para escribir aplicaciones que procesan en paralelo grandes cantidades de datos estructurados y no estructurados en grandes clústeres de máquinas de forma muy confiable y con tolerancia a errores.

![HDI.Pig.Architecture][image-hdi-pig-architecture]

[Apache *Pig*][apachepig-home] proporciona una capa de abstracción sobre el marco de MapReduce basado en Java, que permite a los usuarios analizar datos sin conocer Java o MapReduce. Pig es una plataforma que permite analizar grandes conjuntos de datos mediante un lenguaje fácil de usar denominado  *Pig Latin*, que es un lenguaje de flujo de datos. Pig reduce el tiempo necesario para escribir programas de asignador y de reductor. No requiere ningún conocimiento de Java. Además, tiene la flexibilidad de combinar el código Java con Pig. Se pueden escribir muchos algoritmos complejos en menos de cinco líneas de código Pig legible para el ojo humano. 

Las instrucciones de Pig Latin siguen este flujo general:   

- **Carga**: Datos leídos que se van a manipular desde el sistema de archivos.
- **Transformación**: Manipulación de los datos. 
- **Volcado o almacenamiento**: Datos de salida en la pantalla o almacenamiento para su procesamiento.

Para obtener más información acerca de Pig Latin, consulte el [Manual de referencia de Pig Latin 1][piglatin-manual-1] y el [Manual de referencia de Pig Latin 2][piglatin-manual-2].

## <a id="what"></a>¿Qué hago en este tutorial?
En este tutorial, analizará un archivo de registro de Apache (*sample.log*) para determinar el número de niveles de registro distintos como, por ejemplo, INFO, DEBUG, TRACE, etc. La representación visual de lo que llevará a cabo en este artículo se muestra en las dos ilustraciones siguientes. La primera ilustración muestra un fragmento del archivo sample.log.

![Whole File Sample][image-hdi-log4j-sample]

La segunda ilustración representa el flujo y la transformación de los datos a medida que repasa las líneas de código Pig en el script:

![HDI.PIG.Data.Transformation][image-hdi-pig-data-transformation]

El trabajo de Pig que crea en este tutorial sigue el mismo flujo.

## <a id="data"></a>Identificación de datos para analizar

HDInsight utiliza el contenedor de almacenamiento de blobs de Azure como sistema de archivos predeterminado para clústeres de Hadoop. Se agregan algunos archivos de datos de ejemplo al almacenamiento de blobs como parte del aprovisionamiento de clústeres. Puede usar estos archivos de datos de ejemplo para ejecutar consultas de Hive en el clúster. Si lo desea, también puede cargar su propio archivo de datos en la cuenta de almacenamiento de blobs asociada al clúster. Consulte [Carga de datos en HDInsight][hdinsight-upload-data] for instructions. Para obtener más información sobre el uso del almacenamiento de blobs de Azure con HDInsight, consulte [Uso del almacenamiento de blobs de Azure con HDInsight.][hdinsight-storage].

La sintaxis para tener acceso a los archivos del almacenamiento de blobs es la siguiente:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE] La sintaxis  *wasb://* es la única compatible con la versión 3.0 del clúster de HDInsight. La sintaxis  *asv://* anterior es compatible con los clústeres de HDInsight 2.1 y 1.6, pero no con los de la versión 3.0 y no será compatible con versiones posteriores.

Para tener acceso a un archivo almacenado en el contenedor del sistema de archivos predeterminado desde HDInsight también se puede usar cualquiera de los URI siguientes (con sample.log como ejemplo):  Este archivo es el archivo de datos usado en este tutorial):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
	wasb:///example/data/sample.log
	/example/data/sample.log

Si desea obtener acceso al archivo directamente desde la cuenta de almacenamiento, el nombre de blob del archivo es:

	example/data/sample.log

En este artículo se usa un archivo de ejemplo  *log4j* ue se incluye con clústeres de HDInsight y se almacena en *\example\data\sample.log*. Para obtener información sobre la carga de sus propios archivos de datos, consulte [Carga de datos en HDInsight][hdinsight-upload-data].




## <a id="understand"></a> Descripción de Pig Latin

En esta sección repasará por separado algunas de las instrucciones de Pig Latin y los resultados de ejecutar las instrucciones. En la siguiente sección, ejecutará PowerShell para ejecutar conjuntamente las instrucciones de Pig con el fin de analizar el archivo de registro de ejemplo. Las distintas instrucciones de Pig Latin se deben ejecutar directamente en el clúster de HDInsight.

1. Habilite el Escritorio remoto para el clúster de HDInsight siguiendo las instrucciones dadas en [Conexión a los clústeres de HDInsight con RDP](http://azure.microsoft.com/es-es/documentation/articles/hdinsight-administer-use-management-portal/#rdp). Inicie sesión en el nodo del clúster y, desde el escritorio, haga clic en **Hadoop Command Line**.

2. Desde la línea de comandos, vaya al directorio en donde se ha instalado **Pig**. Tipo:

		C:\apps\dist\hadoop-<version>> cd %pig_home%\bin

3. En el símbolo del sistema, escriba  *pig* y presione Entrar para tener acceso al shell  *grunt*.

		C:\apps\dist\pig-<version>\bin>pig
		...
		grunt>  

4. Introduzca lo siguiente para cargar datos del archivo de ejemplo en el sistema de archivos y muestre los resultados: 

		grunt> LOGS = LOAD 'wasb:///example/data/sample.log';
		grunt> DUMP LOGS;
	
	The output is similar to the following:
	
		(2012-02-05 19:23:50 SampleClass5 [TRACE] verbose detail for id 313393809)
		(2012-02-05 19:23:50 SampleClass6 [DEBUG] detail for id 536603383)
		(2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail for id 564842645)
		(2012-02-05 19:23:50 SampleClass8 [TRACE] verbose detail for id 1929822199)
		(2012-02-05 19:23:50 SampleClass5 [DEBUG] detail for id 1599724386)
		(2012-02-05 19:23:50 SampleClass0 [INFO] everything normal for id 2047808796)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1774407365)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 2099982986)
		(2012-02-05 19:23:50 SampleClass4 [DEBUG] detail for id 180683124)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1072988373)
		(2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail)
		...

5. Revise todas las línea del archivo de datos para buscar una coincidencia en los seis niveles de registro:

		grunt> LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
 
6. Filtre las filas que no tengan una coincidencia y muestre el resultado. Así se deshace de las filas vacías.

		grunt> FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		grunt> DUMP FILTEREDLEVELS;

	La salida será similar a la siguiente:

		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		...

7. Agrupe todos los niveles de registro en su fila correspondiente y muestre el resultado:

		grunt> GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		grunt> DUMP GROUPEDLEVELS;

	La salida será similar a la siguiente:

		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE), ...


8. En cada grupo, cuente las repeticiones de los niveles de registro y muestre el resultado.

		grunt> FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		grunt> DUMP FREQUENCIES;
 
	La salida será similar a la siguiente:

		(INFO,96)
		(WARN,11)
		(DEBUG,434)
		(ERROR,6)
		(FATAL,2)
		(TRACE,816)

9. Ordene las frecuencias en orden descendente y muestre el resultado:

		grunt> RESULT = order FREQUENCIES by COUNT desc;
		grunt> DUMP RESULT;   

	La salida será similar a la siguiente: 

		(TRACE,816)
		(DEBUG,434)
		(INFO,96)
		(WARN,11)
	 	(ERROR,6)
		(FATAL,2)

## <a id="powershell"></a>Envío de trabajos de Pig mediante PowerShell

En esta sección se proporcionan las instrucciones para usar los cmdlets de PowerShell. Antes de pasar a esta sección, debe configurar el entorno local y la conexión con Azure. Para obtener información, consulte [Introducción a HDInsight de Azure][hdinsight-get-started] y [dministración de HDInsight con PowerShell][hdinsight-admin-powershell].


**Ejecutar Pig Latin mediante PowerShell**

1. Abra Windows PowerShell ISE. En la pantalla Inicio de Windows 8, escriba **PowerShell_ISE** y haga clic en **Windows PowerShell ISE**. Consulte [Iniciar Windows PowerShell en Windows 8 y Windows][powershell-start] para obtener más información.

2. En el panel inferior, ejecute el comando siguiente para conectarse a su suscripción de Azure:

		Add-AzureAccount

	Se le pedirá que escriba las credenciales de la cuenta de Azure. Este método de agregar una conexión de suscripción expira y, transcurridas 12 horas, tendrá que volver a ejecutar el cmdlet. 

	> [AZURE.NOTE] Si tiene varias suscripciones de Azure y no desea usar la predeterminada, utilice el cmdlet <strong>Select-AzureSubscription</strong> para seleccionar la suscripción actual.
2. En el panel de scripts, copie y pegue las siguientes líneas:

		$clusterName = "<HDInsightClusterName>" 	#Specify the cluster name
		$statusFolder = "/tutorials/usepig/status"	#Specify the folder to dump results

	Si la carpeta de estado que especifica no existe todavía, el script la creará. 

3. Anexe las líneas siguientes en el panel de scripts. Estas líneas definen la cadena de consulta de Pig Latin y crean una definición de trabajo de Pig:

		# Create the Pig job definition
		$0 = '$0';
		$QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;" 
		
		$pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString -StatusFolder $statusFolder 

	También puede usar el modificador **-File** para especificar un archivo de script de Pig en HDFS. El modificador **-StatusFolder** pone el registro de errores estándar y el archivo de salida estándar en la carpeta.

4. Anexe las siguientes líneas para enviar el trabajo de Pig:
		
		# Submit the Pig job
		Write-Host "Submit the Pig job ..." -ForegroundColor Green
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition  

5. Anexe las siguientes líneas para esperar a que el trabajo de Pig termine:		

		# Wait for the Pig job to complete
		Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

6. Anexe las siguientes líneas para imprimir la salida del trabajo de Pig:
		
		# Print the standard error and the standard output of the Pig job.
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

	> [AZURE.NOTE] Uno de los cmdlets de Get-AzureHDInsightJobOut está comentado para acortar la salida en la siguiente captura de pantalla.   

7. Presione **F5** para ejecutar el script:

	![HDI.Pig.PowerShell][image-hdi-pig-powershell]

	El trabajo de Pig calcula las frecuencias de los diferentes tipos de registro.


## <a id="sdk"></a>Envío de trabajos de Pig mediante el SDK .NET de HDInsight

Siga los pasos indicados aquí para enviar un trabajo de Pig mediante una aplicación C#.   Para obtener instrucciones para crear una aplicación en C# para enviar trabajos de Hadoop, consulte [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs].

1. Cree un certificado autofirmado, instálelo en su estación de trabajo y cárguelo en su suscripción de Azure. Para obtener instrucciones, consulte [Creación de un certificado autofirmado](http://go.microsoft.com/fwlink/?LinkId=511138).

2. Cree una aplicación de consola de Visual Studio e instálela en el paquete de HDInsight. En el menú Herramientas, haga clic en **Administrador de paquetes NuGet** y, a continuación, en **Consola del Administrador de paquetes**. En el símbolo del sistema, introduzca lo siguiente:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

3. Haga doble clic en Program.cs y pegue el código siguiente para enviar un trabajo de Pig. Especifique valores para las variables.

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
		
		namespace SubmitPigJobs
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
					// Set the variables
					string subscriptionID = "<Azure subscription ID>";
					string certFriendlyName = "<certificate friendly name>";
			
					string clusterName = "<HDInsight cluster name>";
		            string statusFolderName = @"/tutorials/usepig/status";
		
		            string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;";
		
		            // Define the Pig job
		            PigJobCreateParameters myJobDefinition = new PigJobCreateParameters()
		            {
		                Query = queryString,
		                StatusFolder = statusFolderName
		            };
		
		            // Get the certificate object from certificate store using the friendly name to identify it
		            X509Store store = new X509Store();
		            store.Open(OpenFlags.ReadOnly);
		            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
		
		            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
		            // Create a hadoop client to connect to HDInsight
		            var jobClient = JobSubmissionClientFactory.Connect(creds);
		
		            // Run the MapReduce job
		            Console.WriteLine("----- Submit the Pig job ...");
		            JobCreationResults mrJobResults = jobClient.CreatePigJob(myJobDefinition);
		
		            // Wait for the job to complete
		            Console.WriteLine("----- Wait for the Pig job to complete ...");
		            WaitForJobCompletion(mrJobResults, jobClient);
		
		            // Display the error log
		            Console.WriteLine("----- The Pig job error log.");
		            using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
		            {
		                var reader = new StreamReader(stream);
		                Console.WriteLine(reader.ReadToEnd());
		            }
		
		            // Display the output log
		            Console.WriteLine("----- The Pig job output log.");
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

## <a id="nextsteps"></a>Pasos siguientes

Aunque Pig le permite realizar análisis de datos, también hay otros lenguajes incluidos con HDInsight que podrían resultarle interesantes. Hive proporciona un lenguaje de consulta tipo SQL que le permite realizar consultas fácilmente en los datos almacenados en HDInsight, mientras que los trabajos de MapReduce escritos en Java le permiten realizar análisis de datos complejos. Para obtener más información, consulte los temas siguientes:


* [Introducción a HDInsight de Azure][hdinsight-get-started]
* [Carga de datos en HDInsight][hdinsight-upload-data]
* [Envío de trabajos de Hadoop mediante programación][hdinsight-submit-jobs]
* [Uso de Hive con HDInsight][hdinsight-use-hive]



[piglatin-manual-1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
[piglatin-manual-2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
[apachepig-home]: http://pig.apache.org/


[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-use-hive]: ../hdinsight-use-hive/

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/#mapreduce-sdk

[Powershell-install-configure]: ../install-configure-powershell/

[powershell-start]: http://technet.microsoft.com/es-es/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png


<!--HONumber=42-->
