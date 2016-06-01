<properties
   pageTitle="Uso del SDK de Java de Análisis de Data Lake para desarrollar aplicaciones | Azure"
   description="Uso del SDK de Java de Análisis de Azure Data Lake para desarrollar aplicaciones"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/22/2016"
   ms.author="edmaca"/>

# Tutorial: Introducción a Análisis de Azure Data Lake mediante SDK de Java

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Aprenda a utilizar el SDK de Java de Análisis de Azure Data Lake para crear una cuenta de Azure Data Lake y realizar operaciones básicas como crear carpetas, cargar y descargar archivos de datos, eliminar la cuenta y actuar con trabajos. Para más información sobre Data Lake, consulte [Análisis de Azure Data Lake](data-lake-analytics-overview.md).

En este tutorial, aprenderá a desarrollar una aplicación de consola Java que contiene ejemplos de tareas administrativas comunes, así como a crear datos de prueba y enviar un trabajo. Para recorrer el mismo tutorial con otras herramientas compatibles, haga clic en las pestañas situadas en la parte superior de esta sección.

[AZURE.INCLUDE [basic-process-include](../../includes/data-lake-analytics-basic-process.md)]

## Requisitos previos

* Kit de desarrollo de Java (JDK) 8 (con Java versión 1.8).
* IntelliJ u otro entorno de desarrollo de Java adecuado. Este requisito es opcional pero recomendable. En las instrucciones siguientes se utiliza IntelliJ.
* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Habilite su suscripción de Azure** para la versión preliminar pública de Análisis de Data Lake. Consulte las [instrucciones](data-lake-analytics-get-started-portal.md#signup).
* Cree una aplicación de Azure Active Directory (AAD) y recupere su **identificador de cliente**, **identificador de inquilino** y **clave**. Para más información sobre las aplicaciones de AAD y obtener instrucciones sobre cómo obtener un id. de cliente, consulte [Creación de aplicación de Active Directory y entidad de servicio mediante el portal](../resource-group-create-service-principal-portal.md). El URI de respuesta y la clave también estarán disponibles desde el portal cuando haya creado la aplicación y generado la clave.

## ¿Cómo se puede autenticar mediante Azure Active Directory?

El siguiente fragmento de código proporciona código para la autenticación **no interactiva**, donde la aplicación proporciona sus propias credenciales.

Para que este tutorial funcione, deberá conceder permiso a su aplicación para crear recursos en Azure. Para este tutorial, se **recomienda encarecidamente** conceder a esta aplicación solo permisos de colaborador para un grupo de recursos nuevo, sin utilizar y vacío en la suscripción de Azure.

## Creación de una aplicación Java

1. Abra IntelliJ y cree un proyecto de Java mediante la plantilla de **aplicación de línea de comandos**.

2. Haga clic con el botón derecho en el proyecto de la izquierda de la pantalla y haga clic en **Add Framework Support** (Agregar compatibilidad con el marco). Elija **Maven** y haga clic en **Aceptar**.

3. Abra el archivo **"pom.xml"** recién creado y agregue el siguiente fragmento de código de texto entre las etiquetas **</version>** y **</project>**:

    NOTA: Este paso es temporal hasta que el SDK de Análisis de Azure Data Lake esté disponible en Maven. Este artículo se actualizará cuando el SDK esté disponible en Maven. Todas las futuras actualizaciones de este SDK estarán disponible mediante Maven.

        <repositories>
        	<repository>
	            <id>adx-snapshots</id>
	            <name>Azure ADX Snapshots</name>
	            <url>http://adxsnapshots.azurewebsites.net/</url>
	            <layout>default</layout>
	            <snapshots>
                	<enabled>true</enabled>
            	</snapshots>
        	</repository>
        	<repository>
	            <id>oss-snapshots</id>
	            <name>Open Source Snapshots</name>
	            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
	            <layout>default</layout>
	            <snapshots>
	                <enabled>true</enabled>
	                <updatePolicy>always</updatePolicy>
	            </snapshots>
        	</repository>
    	</repositories>
    	<dependencies>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-client-authentication</artifactId>
	            <version>1.0.0-SNAPSHOT</version>
	        </dependency>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-mgmt-datalake-store</artifactId>
	            <version>1.0.0-SNAPSHOT</version>
	        </dependency>
	        <dependency>
	            <groupId>com.microsoft.azure</groupId>
	            <artifactId>azure-mgmt-datalake-analytics</artifactId>
	            <version>1.0.0-SNAPSHOT</version>
	        </dependency>
    	</dependencies>


4. Vaya a **Archivo**, **Configuración**, **Compilación**, **Ejecución** e **Implementación**. Seleccione **Herramientas de compilación**, **Maven**, **Importando**. A continuación, marque **Importar proyectos de Maven automáticamente**.

5. Abra el archivo **Main.cs** y sustituya el bloque de código existente por el siguiente código. Especifique también los valores de los parámetros que se mencionan en el fragmento de código, por ejemplo **localFolderPath**, **\_adlsAccountName**, **\_resourceGroupName**, y reemplace los marcadores de posición de **CLIENT-ID**, **CLIENT-SECRET**, **TENANT-ID** y **SUBSCRIPTION-ID**.

	Este código recorre el proceso de creación de cuentas de Almacén de Data Lake y de Análisis de Data Lake, de creación de archivos en el almacén, de ejecución de un trabajo, de obtención del estado del trabajo, de descarga de la salida del trabajo y finalmente de eliminación de la cuenta.
 

		package com.company;

		import com.microsoft.azure.CloudException;
		import com.microsoft.azure.credentials.ApplicationTokenCredentials;
		import com.microsoft.azure.management.datalake.store.*;
		import com.microsoft.azure.management.datalake.store.models.*;
		import com.microsoft.azure.management.datalake.analytics.*;
		import com.microsoft.azure.management.datalake.analytics.models.*;
		import com.microsoft.rest.credentials.ServiceClientCredentials;
		import java.io.*;
		import java.nio.charset.Charset;
		import java.nio.file.Files;
		import java.nio.file.Paths;
		import java.util.ArrayList;
		import java.util.UUID;
		import java.util.List;
		
		public class Main {
		    private static String _adlsAccountName;
		    private static String _adlaAccountName;
		    private static String _resourceGroupName;
		    private static String _location;
		
		    private static String _tenantId;
		    private static String _subId;
		    private static String _clientId;
		    private static String _clientSecret;
		
		    private static DataLakeStoreAccountManagementClient _adlsClient;
		    private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
		    private static DataLakeAnalyticsAccountManagementClient _adlaClient;
		    private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
		    private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
		
		    public static void main(String[] args) throws Exception {
		        _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
		        _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>";
		        _resourceGroupName = "<RESOURCE-GROUP-NAME>";
		        _location = "East US 2";
		
		        _tenantId = "<TENANT-ID>";
		        _subId =  "<SUBSCRIPTION-ID>";
		        _clientId = "<CLIENT-ID>";
		
		        _clientSecret = "<CLIENT-SECRET>"; // TODO: For production scenarios, we recommend that you replace this line with a more secure way of acquiring the application client secret, rather than hard-coding it in the source code.
		
		        String localFolderPath = "C:\\local_path\"; // TODO: Change this to any unused, new, empty folder on your local machine.
		
		        // Authenticate
		        ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
		        SetupClients(creds);
		
		        // Create Data Lake Store and Analytics accounts
		        WaitForNewline("Authenticated.", "Creating NEW accounts.");
		        CreateAccounts();
		        WaitForNewline("Accounts created.", "Displaying accounts.");
		
		        // List Data Lake Store and Analytics accounts that this app can access
		        System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
		        List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
		        for (DataLakeStoreAccount acct : adlsListResult) {
		            System.out.println(acct.getName());
		        }
		        System.out.println(String.format("All ADL Analytics accounts that this app can access in subscription %s:", _subId));
		        List<DataLakeAnalyticsAccount> adlaListResult = _adlaClient.getAccountOperations().list().getBody();
		        for (DataLakeAnalyticsAccount acct : adlaListResult) {
		            System.out.println(acct.getName());
		        }
		        WaitForNewline("Accounts displayed.", "Creating files.");
		
		        // Create a file in Data Lake Store: input1.csv
		        CreateFile("/input1.csv", "123,abc", true);
		        WaitForNewline("File created.", "Submitting a job.");
		
		        // Submit a job to Data Lake Analytics
		        UUID jobId = SubmitJobByScript("@input =  EXTRACT Data string FROM "/input1.csv" USING Extractors.Csv(); OUTPUT @input TO @"/output1.csv" USING Outputters.Csv();", "testJob");
		        WaitForNewline("Job submitted.", "Getting job status.");
		
		        // Wait for job completion and output job status
		        System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
		        System.out.println("Waiting for job completion.");
		        WaitForJob(jobId);
		        System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
		        WaitForNewline("Job completed.", "Downloading job output.");
		
		        // Download job output from Data Lake Store
		        DownloadFile("/output1.csv", localFolderPath + "output1.csv");
		        WaitForNewline("Job output downloaded.", "Deleting file.");
		
		        // Delete file from Data Lake Store
		        DeleteFile("/output1.csv");
		        WaitForNewline("File deleted.", "Deleting account.");
		
		        // Delete account
		        DeleteAccounts();
		        WaitForNewline("Account deleted.", "DONE.");
			}
	
		    //Set up clients
		    public static void SetupClients(ServiceClientCredentials creds)
		    {
		        _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
		        _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
		        _adlaClient = new DataLakeAnalyticsAccountManagementClientImpl(creds);
		        _adlaJobClient = new DataLakeAnalyticsJobManagementClientImpl(creds);
		        _adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClientImpl(creds);
		        _adlsClient.setSubscriptionId(_subId);
		        _adlaClient.setSubscriptionId(_subId);
		    }
		
		    // Helper function to show status and wait for user input
		    public static void WaitForNewline(String reason, String nextAction)
		    {
		        if (nextAction == null)
		            nextAction = "";
		        if (!nextAction.isEmpty())
		        {
		            System.out.println(reason + "\r\nPress ENTER to continue...");
		            try{System.in.read();}
		            catch(Exception e){}
		            System.out.println(nextAction);
		        }
		        else
		        {
		            System.out.println(reason + "\r\nPress ENTER to continue...");
		            try{System.in.read();}
		            catch(Exception e){}
		        }
		    }
		
		    // Create Data Lake Store and Analytics accounts
		    public static void CreateAccounts() throws InterruptedException, CloudException, IOException {
		        // Create ADLS account
		        DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
		        adlsParameters.setLocation(_location);
		
		
		        _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);
		
		        // Create ADLA account
		        DataLakeStoreAccountInfo adlsInfo = new DataLakeStoreAccountInfo();
		        adlsInfo.setName(_adlsAccountName);
		
		        DataLakeStoreAccountInfoProperties adlsInfoProperties = new DataLakeStoreAccountInfoProperties();
		        adlsInfo.setProperties(adlsInfoProperties);
		
		        List<DataLakeStoreAccountInfo> adlsInfoList = new ArrayList<DataLakeStoreAccountInfo>();
		        adlsInfoList.add(adlsInfo);
		
		        DataLakeAnalyticsAccountProperties adlaProperties = new DataLakeAnalyticsAccountProperties();
		        adlaProperties.setDataLakeStoreAccounts(adlsInfoList);
		        adlaProperties.setDefaultDataLakeStoreAccount(_adlsAccountName);
		
		        DataLakeAnalyticsAccount adlaParameters = new DataLakeAnalyticsAccount();
		        adlaParameters.setLocation(_location);
		        adlaParameters.setName(_adlaAccountName);
		        adlaParameters.setProperties(adlaProperties);
		
		        _adlaClient.getAccountOperations().create(_resourceGroupName, _adlaAccountName, adlaParameters);
		    }
		
		    // Create file
		    public static void CreateFile(String path) throws IOException, CloudException {
		        _adlsFileSystemClient.getFileSystemOperations().create(path, _adlsAccountName);
		    }
		
		    // Create file with contents
		    public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
		        byte[] bytesContents = contents.getBytes();
		
		        _adlsFileSystemClient.getFileSystemOperations().create(path, _adlsAccountName, bytesContents, force);
		    }
		
		    // Append to file
		    public static void AppendToFile(String path, String contents) throws IOException, CloudException {
		        byte[] bytesContents = contents.getBytes();
		
		        _adlsFileSystemClient.getFileSystemOperations().append(path, _adlsAccountName, bytesContents);
		    }
		
		    // Concatenate files
		    public static void ConcatenateFiles(List<String> srcFilePaths, String destFilePath) throws IOException, CloudException {
		        _adlsFileSystemClient.getFileSystemOperations().concat(destFilePath, _adlsAccountName, srcFilePaths);
		    }
		
		    // Delete concatenated file
		    public static void DeleteFile(String filePath) throws IOException, CloudException {
		        _adlsFileSystemClient.getFileSystemOperations().delete(filePath, _adlsAccountName);
		    }
		
		    // Get file or directory info
		    public static FileStatusProperties GetItemInfo(String path) throws IOException, CloudException {
		        return _adlsFileSystemClient.getFileSystemOperations().getFileStatus(path, _adlsAccountName).getBody().getFileStatus();
		    }
		
		    // List files and directories
		    public static List<FileStatusProperties> ListItems(String directoryPath) throws IOException, CloudException {
		        return _adlsFileSystemClient.getFileSystemOperations().listFileStatus(directoryPath, _adlsAccountName).getBody().getFileStatuses().getFileStatus();
		    }
		
		    // Download file
		    public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
		        InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(srcPath, _adlsAccountName).getBody();
		
		        PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());
		
		        String fileContents = "";
		        if (stream != null) {
		            Writer writer = new StringWriter();
		
		            char[] buffer = new char[1024];
		            try {
		                Reader reader = new BufferedReader(
		                        new InputStreamReader(stream, "UTF-8"));
		                int n;
		                while ((n = reader.read(buffer)) != -1) {
		                    writer.write(buffer, 0, n);
		                }
		            } finally {
		                stream.close();
		            }
		            fileContents =  writer.toString();
		        }
		
		        pWriter.println(fileContents);
		        pWriter.close();
		    }
		
		    // Submit a U-SQL job by providing script contents.
		    // Returns the job ID
		    public static UUID SubmitJobByScript(String script, String jobName) throws IOException, CloudException {
		        UUID jobId = java.util.UUID.randomUUID();
		        USqlJobProperties properties = new USqlJobProperties();
		        properties.setScript(script);
		        JobInformation parameters = new JobInformation();
		        parameters.setName(jobName);
		        parameters.setJobId(jobId);
		        parameters.setType(JobType.USQL);
		        parameters.setProperties(properties);
		
		        JobInformation jobInfo = _adlaJobClient.getJobOperations().create(_adlaAccountName, jobId, parameters).getBody();
		
		        return jobId;
		    }
		
		    // Submit a U-SQL job by providing a path to the script
		    public static UUID SubmitJobByPath(String scriptPath, String jobName) throws IOException, CloudException {
		        byte[] scriptFileContents = Files.readAllBytes(Paths.get(scriptPath));
		        String script = new String(scriptFileContents, Charset.defaultCharset());
		        return SubmitJobByScript(script, jobName);
		    }
		
		    // Wait for job completion
		    public static JobResult WaitForJob(UUID jobId) throws IOException, CloudException {
		        JobInformation jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName, jobId).getBody();
		        while (jobInfo.getState() != JobState.ENDED)
		        {
		            jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName,jobId).getBody();
		        }
		        return jobInfo.getResult();
		    }
		
		    // Get job status
		    public static String GetJobStatus(UUID jobId) throws IOException, CloudException {
		        JobInformation jobInfo = _adlaJobClient.getJobOperations().get(_adlaAccountName, jobId).getBody();
		        return jobInfo.getState().toValue();
		    }
		
		    // List jobs
		    public static List<JobInformation> ListJobs() throws IOException, CloudException {
		        return _adlaJobClient.getJobOperations().list(_adlaAccountName).getBody();
		    }
		
		    // Delete accounts
		    public static void DeleteAccounts() throws InterruptedException, CloudException, IOException {
		        _adlsClient.getAccountOperations().delete(_resourceGroupName, _adlsAccountName);
		        _adlaClient.getAccountOperations().delete(_resourceGroupName, _adlaAccountName);
		    }
		}

6. Siga las indicaciones para ejecutar y completar la aplicación.


## Consulte también

- Para ver el mismo tutorial con otras herramientas, haga clic en los selectores de pestañas en la parte superior de la página.
- Para ver una consulta más compleja, consulte [Análisis de registros de sitios web mediante Análisis de Azure Data Lake](data-lake-analytics-analyze-weblogs.md).
- Para empezar a desarrollar aplicaciones con U-SQL, consulte [Desarrollo de scripts U-SQL mediante Data Lake Tools for Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para aprender U-SQL, consulte [Tutorial: Introducción al lenguaje U-SQL de Análisis de Azure Data Lake](data-lake-analytics-u-sql-get-started.md) y la página de [referencia del lenguaje U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).
- Para las tareas de administración, consulte [Administración de Análisis de Azure Data Lake mediante el Portal de Azure](data-lake-analytics-manage-use-portal.md).
- Para obtener información general acerca de Análisis de Data Lake, consulte la página de [información general sobre Análisis de Azure Data Lake](data-lake-analytics-overview.md).

<!---HONumber=AcomDC_0518_2016-->