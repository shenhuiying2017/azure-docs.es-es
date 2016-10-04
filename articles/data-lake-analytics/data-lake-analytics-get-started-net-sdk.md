<properties 
   pageTitle="Introducción a Análisis de Azure Data Lake mediante .NET SDK | Azure" 
   description="Aprenda a usar .NET SDK para crear cuentas del Almacén de Data Lake, crear trabajos de Análisis de Data Lake y enviar trabajos escritos en U-SQL. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/23/2016"
   ms.author="edmaca"/>

# Tutorial: Introducción a Análisis de Azure Data Lake mediante .NET SDK

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Aprenda a usar el SDK de .NET de Azure para enviar trabajos escritos en [U-SQL](data-lake-analytics-u-sql-get-started.md) a Data Lake Analytics. Para más información acerca de Análisis de Data Lake, consulte [Información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

En este tutorial desarrollará una aplicación de consola en C# para enviar un trabajo de U-SQL que lee un archivo de valores separados por tabulaciones (TSV) y lo convierte en un archivo de valores separados por comas (CSV). Para recorrer el mismo tutorial con otras herramientas compatibles, haga clic en las pestañas de la parte superior de este artículo.

##Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Visual Studio 2015, Visual Studio 2013 Update 4 o Visual Studio 2012 con Visual C++ instalado**.
- **SDK de Microsoft Azure para .NET versión 2.5 o posterior**. Instálelo usando el [instalador de plataforma web](http://www.microsoft.com/web/downloads/platform.aspx).
- **Una cuenta de Análisis de Azure Data Lake**. Consulte [Manage Data Lake Analytics using Azure .NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md) (Administración de Data Lake Analytics el SDK de .NET de Azure).

##Creación de una aplicación de consola

En este tutorial, va a procesar algunos registros de búsqueda. El registro de búsqueda se puede almacenar en el Almacén de Data Lake o en el almacenamiento de blobs de Azure.

Se puede encontrar un registro de búsqueda de ejemplo en un contenedor de blobs de Azure público. En la aplicación, descargará el archivo en la estación de trabajo y después cargará el archivo en la cuenta predeterminada de Data Lake Store de la cuenta de Data Lake Analytics.

**Para crear una aplicación**

1. Abra Visual Studio.
2. Cree una aplicación de consola en C#
3. Abra la consola de administración de paquetes NuGet y ejecute el siguiente comando:

        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package WindowsAzure.Storage

4. Agregue un nuevo archivo al proyecto llamado **SampleUSQLScript.txt** y después pegue el siguiente script U-SQL. Los trabajos de Análisis de Data Lake se escriben en el lenguaje U-SQL. Para obtener más información sobre U-SQL, consulte [Introducción al lenguaje U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348) y [Referencia sobre el lenguaje U-SQL](data-lake-analytics-u-sql-get-started.md).

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	Este script U-SQL lee el archivo de datos de origen mediante **Extractors.Tsv()** y después crea un archivo csv mediante **Outputters.Csv()**.
    
    En el programa en C#, deberá preparar el archivo **/Samples/Data/SearchLog.tsv** y la carpeta **/Output/**.
	
	Es más fácil usar rutas de acceso relativas para los archivos almacenados en cuentas predeterminadas de Data Lake. También puede usar rutas de acceso absolutas. Por ejemplo:
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Debe usar rutas de acceso absolutas para acceder a los archivos de cuentas de almacenamiento vinculadas. La sintaxis de los archivos almacenados en la cuenta de Almacenamiento de Azure vinculada es:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

	>[AZURE.NOTE] Actualmente no hay ningún problema conocido con el servicio de Azure Data Lake. Si la aplicación de ejemplo se interrumpe o encuentra un error, es posible que deba eliminar manualmente las cuentas de Almacén de Data Lake y Análisis de Data Lake que crea el script. Si no conoce el portal, la guía [Administración de Azure Data Lake Analytics mediante Azure Portal](data-lake-analytics-manage-use-portal.md) le ayudará a empezar.
       
5. En Program.cs, pegue el siguiente código:

		using System;
		using System.IO;
		using System.Collections.Generic;
		using System.Threading;
		using Microsoft.Rest;
		using Microsoft.Rest.Azure.Authentication;
		using Microsoft.Azure.Management.DataLake.Store;
		using Microsoft.Azure.Management.DataLake.StoreUploader;
		using Microsoft.Azure.Management.DataLake.Analytics;
		using Microsoft.Azure.Management.DataLake.Analytics.Models;
		using Microsoft.WindowsAzure.Storage.Blob;

		namespace SdkSample
		{
		  class Program
		  {
			private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
			private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
			private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

			private static string _adlaAccountName = "<Enter an Existing Data Lake Analytics Account Name>";
			private static string _adlsAccountName = "<Enter the default Data Lake Store Account Name>";

			private static DataLakeAnalyticsAccountManagementClient _adlaClient;
			private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
			private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
		
		    private static void Main(string[] args)
		    {
				string localFolderPath = @"c:\temp";

				// Connect to Azure
				var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

				SetupClients(creds, SUBSCRIPTIONID);

				// Transfer the source file from a public Azure Blob container to Data Lake Store.
				CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://adltutorials.blob.core.windows.net/adls-sample-data/SearchLog.tsv"));
				blob.DownloadToFile(localFolderPath + "SearchLog.tsv", FileMode.Create); // from WASB
				UploadFile(localFolderPath + "SearchLog.tsv", "/Samples/Data/SearchLog.tsv"); // to ADLS
				WaitForNewline("Source data file prepared.", "Submitting a job.");


				// Submit the job
				Guid jobId = SubmitJobByPath(localFolderPath + "SampleUSQLScript.txt", "My First ADLA Job");
				WaitForNewline("Job submitted.", "Waiting for job completion.");

				// Wait for job completion
				WaitForJob(jobId);
				WaitForNewline("Job completed.", "Downloading job output.");

				// Download job output
				DownloadFile(@"/Output/SearchLog-from-Data-Lake.csv", localFolderPath + "SearchLog-from-Data-Lake.csv");
		
		      	WaitForNewline("Job output downloaded. You can now exit.");
		    }
		
			public static ServiceClientCredentials AuthenticateAzure(
				string domainName,
				string nativeClientAppCLIENTID)
			{
				// User login via interactive popup
				SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
				// Use the client ID of an existing AAD "Native Client" application.
				var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
				return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
			}

			public static void SetupClients(ServiceClientCredentials tokenCreds, string subscriptionId)
			{
				_adlaClient = new DataLakeAnalyticsAccountManagementClient(tokenCreds);
				_adlaClient.SubscriptionId = subscriptionId;

				_adlaJobClient = new DataLakeAnalyticsJobManagementClient(tokenCreds);

				_adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
			}

			public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
			{
				var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
				var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
				var uploader = new DataLakeStoreUploader(parameters, frontend);
				uploader.Execute();
			}

			public static void DownloadFile(string srcPath, string destPath)
			{
				var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
				var fileStream = new FileStream(destPath, FileMode.Create);

				stream.CopyTo(fileStream);
				fileStream.Close();
				stream.Close();
			}

			// Helper function to show status and wait for user input
			public static void WaitForNewline(string reason, string nextAction = "")
			{
				Console.WriteLine(reason + "\r\nPress ENTER to continue...");

				Console.ReadLine();

				if (!String.IsNullOrWhiteSpace(nextAction))
					Console.WriteLine(nextAction);
			}


			// List all Data Lake Analytics accounts within the subscription
			public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
			{
				var response = _adlaClient.Account.List();
				var accounts = new List<DataLakeAnalyticsAccount>(response);

				while (response.NextPageLink != null)
				{
					response = _adlaClient.Account.ListNext(response.NextPageLink);
					accounts.AddRange(response);
				}

				Console.WriteLine("You have %i Data Lake Analytics account(s).", accounts.Count);
				for (int i = 0; i < accounts.Count; i++)
				{
					Console.WriteLine(accounts[i].Name);
				}

				return accounts;
			}
			public static Guid SubmitJobByPath(string scriptPath, string jobName)
			{
				var script = File.ReadAllText(scriptPath);

				var jobId = Guid.NewGuid();
				var properties = new USqlJobProperties(script);
				var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
				var jobInfo = _adlaJobClient.Job.Create(_adlaAccountName, jobId, parameters);

				return jobId;
			}

			public static JobResult WaitForJob(Guid jobId)
			{
				var jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
				while (jobInfo.State != JobState.Ended)
				{
					jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
				}
				return jobInfo.Result.Value;
			}
		  }
		}

6. Presione **F5** para ejecutar la aplicación. El resultado es como el que aparece a continuación:

	![Salida del SDK de .NET de U-SQL del trabajo de Azure Data Lake Analytics](./media/data-lake-analytics-get-started-net-sdk/data-lake-analytics-dotnet-job-output.png)

7. Compruebe el archivo de salida. El nombre de archivo y la ruta de acceso predeterminados son c:\\Temp\\SearchLog-from-Data-Lake.csv.

## Consulte también

- Para ver el mismo tutorial con otras herramientas, haga clic en los selectores de pestañas en la parte superior de la página.
- Para ver una consulta más compleja, consulte la página sobre el [análisis de registros de sitio web mediante Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Para empezar a desarrollar aplicaciones con U-SQL, consulte [Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para aprender U-SQL, consulte el [Tutorial: Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md) y la página de [referencia del lenguaje U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).
- Para conocer las tareas de administración, consulte [Administración de Azure Data Lake Analytics mediante el Azure Portal](data-lake-analytics-manage-use-portal.md).
- Para obtener información general acerca de Análisis de Data Lake, consulte la página de [información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

<!---HONumber=AcomDC_0928_2016-->