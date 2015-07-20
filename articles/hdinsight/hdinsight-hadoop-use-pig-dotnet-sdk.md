<properties
   pageTitle="Uso de Pig con Hadoop con .NET en HDInsight | Microsoft Azure"
   description="Aprenda a usar el SDK de .NET de Hadoop para enviar trabajos de Pig a Hadoop en HDInsight."
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/06/2015"
   ms.author="larryfr"/>

#Ejecución de trabajos de Pig con el SDK de .NET para Hadoop en HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Este documento proporciona un ejemplo de uso del SDK de .NET para Hadoop para enviar trabajos de Pig a un Hadoop en clúster de HDInsight.

El SDK .NET de HDInsight proporciona bibliotecas de cliente .NET que facilitan el trabajo con los clústeres de HDInsight en .NET. Pig le permite crear operaciones de MapReduce al modelar una serie de transformaciones de datos. Aprenderá a utilizar una aplicación básica de C# para enviar un trabajo de Pig para un clúster de HDInsight.

##<a id="prereq"></a>Requisitos previos

Necesitará lo siguiente para completar los pasos de este artículo.

* Un clúster de HDInsight de Azure (Hadoop en HDInsight) (Windows o Linux)

* Visual Studio 2012 o 2013

##<a id="certificate"></a>Creación de un certificado de administración

Para autenticar la aplicación a HDInsight de Azure, debe crear un certificado autofirmado, instalarlo en su estación de trabajo de desarrollo y también cargarlo en su suscripción de Azure.

Para obtener instrucciones sobre cómo hacerlo, consulte [Creación de un certificado autofirmado](http://go.microsoft.com/fwlink/?LinkId=511138).

> [AZURE.NOTE]Al crear el certificado, asegúrese de tener en cuenta el nombre descriptivo que utilice, ya que lo utilizará más adelante.

##<a id="subscriptionid"></a>Búsqueda del identificador de suscripción

Cada suscripción de Azure se identifica por un valor GUID, conocido como el identificador de suscripción. Utilice los pasos siguientes para encontrar este valor.

1. Visite la [consola de administración de Azure](https://manage.windowsazure.com/).

2. En la barra de la izquierda del portal, seleccione **Configuración**.

3. En la información que se presenta a la derecha de la página, busque la suscripción que desea usar y anote el valor de la columna **Id. de suscripción**.

Guarde el identificador de suscripción, pues se utilizará más adelante.

##<a id="create"></a>Creación de la aplicación

1. Apertura de Visual Studio 2012 o 2013

2. En el menú **Archivo**, seleccione **Nuevo** y, a continuación, seleccione **Proyecto**.

3. Para el nuevo proyecto, escriba o seleccione los valores siguientes.

	<table>
<tr>
<th>Propiedad</th>
<th>Valor</th>
</tr>
<tr>
<th>Categoría</th>
<th>Plantillas/Visual C#/Windows</th>
</tr>
<tr>
<th>Plantilla</th>
<th>Aplicación de consola</th>
</tr>
<tr>
<th>Nombre</th>
<th>SubmitPigJob</th>
</tr>
</table>

4. Haga clic en **Aceptar** para crear el proyecto.

5. En el menú **Herramientas**, seleccione **Administrador de paquetes de biblioteca** o **Administrador de paquetes de Nuget** y, a continuación, seleccione **Consola del administrador de paquetes**.

6. Ejecute el siguiente comando en la consola para instalar los paquetes de SDK de .NET.

		Install-Package Microsoft.Windowsazure.Management.HDInsight

7. En el Explorador de soluciones, haga doble clic en **Program.cs** para abrirlo. Reemplace el código existente por lo siguiente.

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

		namespace SubmitPigJob
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
		            // Get the subscription ID
		            string subscriptionID = PromptForInput("Enter your Azure Subscription ID:");

		            // Get the certificate name
		            string certFriendlyName = PromptForInput("Enter the management certificate name:");

		            // Get the cluster name
		            string clusterName = PromptForInput("Enter the HDInsight cluster name:");

		            // Set the folder that job status is written to
		            string statusFolderName = @"/tutorials/usepig/status";

		            // The Pig Latin statements to run
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
		            JobCreationResults mrJobResults = jobClient.CreatePigJob		(myJobDefinition);

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

		        private static string PromptForInput(string message)
		        {
		            Console.WriteLine(message);
		            return Console.ReadLine();
		        }
		    }
		}


7. Guarde el archivo .

##<a id="run"></a>Ejecución de la aplicación.

Utilice **F5** para iniciar la aplicación. Cuando se le indique, introduzca el **identificador de suscripción**, el **nombre descriptivo del certificado** y el **nombre del clúster de HDInsight**. La aplicación generará varias líneas de información mientras se ejecuta, terminando con algo parecido a lo siguiente.

```
----- The Pig job output log.
(TRACE,816)
(DEBUG,434)
(INFO,96)
(WARN,11)
(ERROR,6)
(FATAL,2)

----- Press ENTER to continue.
```

Presione **ENTRAR** para salir de la aplicación.

##<a id="summary"></a>Resumen

Como puede observar, el SDK de .NET para Hadoop le permite crear aplicaciones .NET que envíen trabajos de Pig a un clúster de HDInsight, supervisar el estado del trabajo y recuperar el resultado.

##<a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre Pig en HDInsight.

* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)

Para obtener información sobre otras maneras en que puede trabajar con Hadoop en HDInsight.

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)

* [Uso de MapReduce con Hadoop en HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=July15_HO2-->