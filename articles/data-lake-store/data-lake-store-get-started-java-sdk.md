<properties
   pageTitle="Uso del SDK de Java del Almacén de Data Lake para desarrollar aplicaciones | Azure"
   description="Uso del SDK de Java del Almacén de Azure Data Lake para desarrollar aplicaciones"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/12/2016"
   ms.author="nitinme"/>

# Introducción al Almacén de Azure Data Lake mediante Java

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [SDK de Java](data-lake-store-get-started-java-sdk.md)
- [API DE REST](data-lake-store-get-started-rest-api.md)
- [CLI de Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Aprenda a utilizar el SDK de Java del Almacén de Azure Data Lake para crear una cuenta de Azure Data Lake y realizar operaciones básicas, como crear carpetas, cargar y descargar archivos de datos, eliminar la cuenta, etc. Para obtener más información sobre Data Lake, consulte [Almacén de Azure Data Lake](data-lake-store-overview.md).

## SDK de Java del Almacén de Azure Data Lake

Los vínculos siguientes proporcionan la ubicación de descarga del SDK de Java del Almacén de Data Lake y la referencia del SDK de Java. Para este tutorial no es preciso descargar el SDK ni seguir el documento de referencia. Estos vínculos solo tienen un fin informativo.

* El código fuente del SDK de Java del Almacén de Data Lake está disponible en [GitHub](https://github.com/Azure/azure-sdk-for-java).
* La referencia del SDK de Java para el Almacén de Data Lake está disponible en [https://azure.github.io/azure-sdk-for-java/](https://azure.github.io/azure-sdk-for-java/).

## Requisitos previos

* Kit de desarrollo de Java (JDK) 8 (con Java versión 1.8).
* IntelliJ u otro entorno de desarrollo de Java adecuado. Este requisito es opcional pero recomendable. En las instrucciones siguientes se utiliza IntelliJ.
* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Habilite su suscripción de Azure** para la versión de vista previa pública del Almacén de Data Lake. Consulte las [instrucciones](data-lake-store-get-started-portal.md#signup).
* **Creación de una aplicación de Azure Active Directory**. Hay dos formas de realizar la autenticación con Azure Active Directory: **interactiva** y **no interactiva**, cada una con diferentes requisitos previos.
	* **Para la autenticación interactiva**: en Azure Active Directory, debe crear una **aplicación cliente nativa**. Cuando haya creado la aplicación, recupere los siguientes valores relacionados con esta.
		- Obtenga el **id. de cliente** y el **URI de redirección** de la aplicación.
		- Establecimiento de permisos delegados

	* **Para la autenticación no interactiva** (la que se usa en este artículo): en Azure Active Directory, debe crear una **aplicación web**. Cuando haya creado la aplicación, recupere los siguientes valores relacionados con esta.
		- Obtenga el **id. de cliente**, el **secreto de cliente** y el **URI de redirección** de la aplicación.
		- Establecimiento de permisos delegados
		- Asigne la aplicación de Azure Active Directory a un rol. El rol puede encontrarse al nivel del ámbito en el que quiere conceder el permiso a la aplicación de Azure Active Directory. Por ejemplo, puede asignar la aplicación en el nivel de suscripción o en el nivel de un grupo de recursos. 

	Consulte [Creación de aplicación de Active Directory y entidad de servicio mediante el portal](../resource-group-create-service-principal-portal.md) para obtener instrucciones acerca de cómo recuperar estos valores, establecer los permisos y asignar roles.

## ¿Cómo se puede autenticar mediante Azure Active Directory?

El siguiente fragmento de código proporciona código para la autenticación **no interactiva**, donde la aplicación proporciona sus propias credenciales.

Para que este tutorial funcione, deberá conceder permiso a su aplicación para crear recursos en Azure. Para este tutorial, se **recomienda encarecidamente** conceder a esta aplicación solo permisos de colaborador para un grupo de recursos nuevo, sin utilizar y vacío en la suscripción de Azure.

## Creación de una aplicación Java

1. Abra IntelliJ y cree un proyecto de Java mediante la plantilla de **aplicación de línea de comandos**. Complete el asistente para crear el proyecto.

2. Haga clic con el botón derecho en el proyecto de la izquierda de la pantalla y haga clic en **Add Framework Support** (Agregar compatibilidad con marco). Elija **Maven** y haga clic en **Aceptar**.

3. Abra el archivo **"pom.xml"** recién creado y agregue el siguiente fragmento de código de texto entre las etiquetas **</version>** y **</project>**:

    >[AZURE.NOTE] Este paso es temporal hasta que el SDK del almacén de Azure Data Lake esté disponible en Maven. Este artículo se actualizará cuando el SDK esté disponible en Maven. Todas las futuras actualizaciones de este SDK estarán disponibles mediante Maven.

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
    	</dependencies>


4. Vaya a **File** (Archivo), **Settings** (Configuración), **Build, Execution, and Deployment** (Compilación, ejecución e implementación). Expanda **Build Tools** (Herramientas de compilación), **Maven** e **Importing** (Importación). Active la casilla **Import Maven projects automatically** (Importar proyectos de Maven automáticamente). Haga clic en **Apply** (Aplicar) y luego en **OK** (Aceptar).

5. En el panel izquierdo, navegue a **src**, **main**, **java**, **<nombre del paquete>**, abra **Main.java** y reemplace el bloque de código existente por el siguiente. Especifique también los valores de los parámetros que se mencionan en el fragmento de código, por ejemplo **localFolderPath**, **\_adlsAccountName**, **\_resourceGroupName**, y reemplace los marcadores de posición de **CLIENT-ID**, **CLIENT-SECRET**, **TENANT-ID** y **SUBSCRIPTION-ID**.

    Este código recorre los siguientes procesos: la creación de una cuenta del Almacén de Data Lake, la creación de archivos en el almacén, la concatenación de archivos, la descarga de archivos y, finalmente, la eliminación de la cuenta.

        package com.company;
        
        import com.microsoft.azure.CloudException;
        import com.microsoft.azure.credentials.ApplicationTokenCredentials;
        import com.microsoft.azure.management.datalake.store.*;
        import com.microsoft.azure.management.datalake.store.models.*;
        import com.microsoft.rest.credentials.ServiceClientCredentials;
        import java.io.*;
        import java.nio.charset.Charset;
        import java.util.ArrayList;
        import java.util.List;
        
        public class Main {
            private static String _adlsAccountName;
            private static String _resourceGroupName;
            private static String _location;
        
            private static String _tenantId;
            private static String _subId;
            private static String _clientId;
            private static String _clientSecret;
        
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
        
            public static void main(String[] args) throws Exception {
                _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
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
        
                // Create Data Lake Store account
                WaitForNewline("Authenticated.", "Creating NEW account.");
                CreateAccount();
                WaitForNewline("Account created.", "Displaying account(s).");
        
                // List Data Lake Store accounts that this app can access
                System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subId));
                List<DataLakeStoreAccount> adlsListResult = _adlsClient.getAccountOperations().list().getBody();
                for (DataLakeStoreAccount acct : adlsListResult) {
                    System.out.println(acct.getName());
                }
                WaitForNewline("Account(s) displayed.", "Creating files.");
        
                // Create two files in Data Lake Store: file1.csv and file2.csv
                CreateFile("/file1.csv", "123,abc", true);
                CreateFile("/file2.csv", "456,def", true);
                WaitForNewline("Files created.", "Concatenating files.");
        
                // Concatenate two files in Data Lake Store
                List<String> srcFilePaths = new ArrayList<String>();
                srcFilePaths.add("/file1.csv");
                srcFilePaths.add("/file2.csv");
                ConcatenateFiles(srcFilePaths, "/input.csv");
                WaitForNewline("Files concatenated.", "Downloading file.");
        
                // Download file from Data Lake Store
                DownloadFile("/input.csv", localFolderPath + "input.csv");
                WaitForNewline("File downloaded.", "Deleting file.");
        
                // Delete file from Data Lake Store
                DeleteFile("/input.csv");
                WaitForNewline("File deleted.", "Deleting account.");
        
                // Delete account
                DeleteAccount();
                WaitForNewline("Account deleted.", "DONE.");
            }
        
            //Set up clients
            public static void SetupClients(ServiceClientCredentials creds)
            {
                _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
                _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClientImpl(creds);
        
                _adlsClient.setSubscriptionId(_subId);
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
        
            // Create Data Lake Store account
            public static void CreateAccount() throws InterruptedException, CloudException, IOException {
                DataLakeStoreAccount adlsParameters = new DataLakeStoreAccount();
                adlsParameters.setLocation(_location);
        
                _adlsClient.getAccountOperations().create(_resourceGroupName, _adlsAccountName, adlsParameters);
            }
        
            // Create file
            public static void CreateFile(String path) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path);
            }
        
            // Create file with contents
            public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().create(_adlsAccountName, path, bytesContents, force);
            }
        
            // Append to file
            public static void AppendToFile(String path, String contents) throws IOException, CloudException {
                byte[] bytesContents = contents.getBytes();
        
                _adlsFileSystemClient.getFileSystemOperations().append(_adlsAccountName, path, bytesContents);
            }
        
            // Concatenate files
            public static void ConcatenateFiles(List<String> srcFilePaths, String destFilePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().concat(_adlsAccountName, destFilePath, srcFilePaths);
            }
        
            // Delete concatenated file
            public static void DeleteFile(String filePath) throws IOException, CloudException {
                _adlsFileSystemClient.getFileSystemOperations().delete(_adlsAccountName, filePath);
            }
        
            // Get file or directory info
            public static FileStatusProperties GetItemInfo(String path) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().getFileStatus(_adlsAccountName, path).getBody().getFileStatus();
            }
        
            // List files and directories
            public static List<FileStatusProperties> ListItems(String directoryPath) throws IOException, CloudException {
                return _adlsFileSystemClient.getFileSystemOperations().listFileStatus(_adlsAccountName, directoryPath).getBody().getFileStatuses().getFileStatus();
            }
        
            // Download file
            public static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
                InputStream stream = _adlsFileSystemClient.getFileSystemOperations().open(_adlsAccountName, srcPath).getBody();
        
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
        
            // Delete account
            public static void DeleteAccount() throws InterruptedException, CloudException, IOException {
                _adlsClient.getAccountOperations().delete(_resourceGroupName, _adlsAccountName);
            }
        }


6. Ejecute la aplicación. Siga las indicaciones para ejecutar y completar la aplicación.

## Pasos siguientes

- [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
- [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0518_2016-->