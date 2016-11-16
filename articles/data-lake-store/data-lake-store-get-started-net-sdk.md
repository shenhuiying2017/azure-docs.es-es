---
title: Uso del SDK de .NET de Data Lake Store para desarrollar aplicaciones | Microsoft Docs
description: "Uso de SDK de .NET del Almacén de Azure Data Lake para desarrollar aplicaciones"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 47f8601471c6b1f6da5d57d1f30da51af76fba85


---
# <a name="get-started-with-azure-data-lake-store-using-net-sdk"></a>Introducción al Almacén de Azure Data Lake mediante SDK de .NET
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [SDK de Java](data-lake-store-get-started-java-sdk.md)
> * [API DE REST](data-lake-store-get-started-rest-api.md)
> * [CLI de Azure](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> 
> 

Aprenda a usar el [SDK de .NET de Azure Data Lake Store](https://msdn.microsoft.com/library/mt581387.aspx) para realizar operaciones básicas como crear carpetas, cargar y descargar archivos de datos, etc. Para más información sobre Data Lake, consulte [Azure Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Requisitos previos
* **Visual Studio 2013 o 2015**. Las instrucciones siguientes usan Visual Studio 2015.
* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Cuenta del Almacén de Azure Data Lake**. Para obtener instrucciones acerca de cómo crear una cuenta, consulte [Introducción a Azure Data Lake Store mediante Azure Portal](data-lake-store-get-started-portal.md)
* **Cree una aplicación de Azure Active Directory**. Utilice la aplicación Azure AD para autenticar la aplicación Data Lake Store con Azure AD. Existen diferentes enfoques para realizar la autenticación con Azure AD, que son la **autenticación de usuario final** o la **autenticación de servicio a servicio**. Para instrucciones y más información acerca de cómo realizar la autenticación, consulte [Authenticate with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md)(Autenticación con Data Lake Store mediante Azure Active Directory).

## <a name="create-a-net-application"></a>Creación de una aplicación .NET
1. Abra Visual Studio y cree una aplicación de consola.
2. En el menú **Archivo**, haga clic en **Nuevo** y en **Proyecto**.
3. En **Nuevo proyecto**, escriba o seleccione los siguientes valores:
   
   | Propiedad | Valor |
   | --- | --- |
   | Categoría |Plantillas/Visual C#/Windows |
   | Plantilla |Aplicación de consola |
   | Nombre |CreateADLApplication |
4. Haga clic en **Aceptar** para crear el proyecto.
5. Agregue los paquetes de Nuget al proyecto.
   
   1. Haga clic con el botón derecho en el Explorador de soluciones y haga clic en **Administrar paquetes de NuGet**.
   2. En la pestaña **Administrador de paquetes NuGet**, asegúrese de que la opción **Origen del paquete** esté establecida en **nuget.org** y que esté activada la casilla **Incluir versión previa**.
   3. Busque e instale los siguientes paquetes NuGet:
      
      * `Microsoft.Azure.Management.DataLake.Store` - En este tutorial se usa v0.12.5 (versión preliminar).
      * `Microsoft.Azure.Management.DataLake.StoreUploader` - En este tutorial se usa v0.10.6 (versión preliminar).
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - En este tutorial se usa v2.2.8 (versión preliminar).
        
        ![Agregue un origen de Nuget](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Create a new Azure Data Lake account")
   4. Cierre el **Administrador de paquetes Nuget**.
6. Abra **Program.cs**, elimine el código existente e incluya las siguientes instrucciones para agregar referencias a espacios de nombres.
   
        using System;
        using System.Threading;
   
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.StoreUploader;
7. Declare las variables como se muestra a continuación y especifique los valores del nombre de Data Lake Store y del nombre del grupo de recursos que ya existen. Además, asegúrese de que la ruta de acceso local y el nombre de archivo que proporcione aquí deben existir en el equipo. Agregue el siguiente fragmento de código después de las declaraciones de espacios de nombres.
   
        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
   
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = localFolderPath + "file.txt"; // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = remoteFolderPath + "file.txt";
                }
            }
        }

En las restantes secciones de este artículo, se puede ver cómo se utilizan los métodos .NET disponibles para realizar operaciones como la autenticación, la carga de archivos, etc.

## <a name="authentication"></a>Autenticación
### <a name="if-you-are-using-enduser-authentication-recommended-for-this-tutorial"></a>Si utiliza la autenticación de usuario final (recomendada para este tutorial)
Se utiliza con una aplicación "Native Client" de Azure AD existente; se proporciona una a continuación. Para ayudarle a completar este tutorial más rápido, se recomienda que utilice este enfoque.

    // User login via interactive popup
    // Use the client ID of an existing AAD "Native Client" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.
    var nativeClientApp_clientId = "1950a258-227b-4e31-a9cf-717495945fc2";
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(domain, activeDirectoryClientSettings).Result;

Dos cosas que conviene saber acerca del fragmento de código anterior.

* Para ayudarle a completar este tutorial más rápido, este fragmento de código usa un identificador de dominio y cliente de Azure AD que está disponible de manera predeterminada para todas las suscripciones de Azure. Por lo tanto, puede **usar este fragmento de código tal cual en la aplicación**.
* Sin embargo, si desea utilizar su propio identificador de cliente de dominio y de aplicación de Azure AD, debe crear una aplicación nativa de Azure AD y, después, utilizar el dominio de Azure AD, el identificador de cliente y el identificador URI de redirección para la aplicación que ha creado. Para obtener instrucciones, consulte [Creación de una aplicación de Active Directory](../resource-group-create-service-principal-portal.md#create-an-active-directory-application) .

> [!NOTE]
> Las instrucciones de los vínculos anteriores son para una aplicación web de Azure AD. Sin embargo, los pasos son exactamente iguales, aunque decida crear una aplicación cliente nativa. 
> 
> 

### <a name="if-you-are-using-servicetoservice-authentication-with-client-secret"></a>Si utiliza la autenticación de servicio a servicio con el secreto de cliente
El siguiente fragmento de código se puede utilizar para autenticar la aplicación de forma no interactiva, para lo que se usa el secreto o la clave del cliente para una identidad de servicio o aplicación. Utilícelo con una [aplicación de "aplic. web" de Azure AD](../resource-group-create-service-principal-portal.md).

    // Service principal / appplication authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(domain, clientCredential).Result;

### <a name="if-you-are-using-servicetoservice-authentication-with-certificate"></a>Si utilice autenticación de servicio a servicio con certificado
Como alternativa, el siguiente fragmento de código puede utilizarse para autenticar la aplicación de forma no interactiva, para lo que se usa el secreto de cliente o la clave para una identidad de servicio o aplicación. Utilícelo con una [aplicación de "aplic. web" de Azure AD](../resource-group-create-service-principal-portal.md).

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var domain = "<AAD-directory-domain>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(domain, clientAssertionCertificate).Result;

## <a name="create-client-objects"></a>Creación de objetos de cliente
El fragmento de código siguiente crea los objetos de cliente del sistema de archivos y cuenta de Data Lake Store, que se usan para emitir solicitudes al servicio.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds);
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

    _adlsClient.SubscriptionId = _subId;

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Enumeración de todas las cuentas de Data Lake Store de una suscripción
El siguiente fragmento de código enumera todas las cuentas de Data Lake Store de una suscripción de Azure dada.

    // List all ADLS accounts within the subscription
    public static List<DataLakeStoreAccount> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List();
        var accounts = new List<DataLakeStoreAccount>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="create-a-directory"></a>Creación de directorios
El siguiente fragmento muestra un método `CreateDirectory` que puede utilizar para crear un directorio en una cuenta de Data Lake Store.

    // Create a directory
    public static void CreateDirectory(string path)
    {
        _adlsFileSystemClient.FileSystem.Mkdirs(_adlsAccountName, path);
    }

## <a name="upload-a-file"></a>Cargar un archivo
El siguiente fragmento muestra un método `UploadFile` que puede utilizar para cargar archivos en una cuenta de Data Lake Store.

    // Upload a file
    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
    {
        var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
        var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
        var uploader = new DataLakeStoreUploader(parameters, frontend);
        uploader.Execute();
    }

`DataLakeStoreUploader` admite la carga y descarga recursiva entre una ruta de acceso de un archivo local y la ruta de acceso de un archivo de Data Lake Store.    

## <a name="get-file-or-directory-info"></a>Obtención de información de un archivo o directorio
El siguiente fragmento muestra un método `GetItemInfo` que puede utilizar para recuperar la información sobre un archivo o directorio disponible en Data Lake Store. 

    // Get file or directory info
    public static FileStatusProperties GetItemInfo(string path)
    {
        return _adlsFileSystemClient.FileSystem.GetFileStatus(_adlsAccountName, path).FileStatus;
    }

## <a name="list-file-or-directories"></a>Enumeración de archivos o directorios
El siguiente fragmento muestra un método `ListItem` que puede utilizar para enumerar el archivo y los directorios de una cuenta de Data Lake Store.

    // List files and directories
    public static List<FileStatusProperties> ListItems(string directoryPath)
    {
        return _adlsFileSystemClient.FileSystem.ListFileStatus(_adlsAccountName, directoryPath).FileStatuses.FileStatus.ToList();
    }

## <a name="concatenate-files"></a>Concatenación de archivos
El siguiente fragmento muestra un método `ConcatenateFiles` que se utiliza para concatenar archivos. 

    // Concatenate files
    public static void ConcatenateFiles(string[] srcFilePaths, string destFilePath)
    {
        _adlsFileSystemClient.FileSystem.Concat(_adlsAccountName, destFilePath, srcFilePaths);
    }

## <a name="append-to-a-file"></a>Anexión a un archivo
El siguiente fragmento muestra un método `AppendToFile` que se utiliza para anexar datos a un archivo que ya está almacenado en una cuenta de Data Lake Store.

    // Append to file
    public static void AppendToFile(string path, string content)
    {
        var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));

        _adlsFileSystemClient.FileSystem.Append(_adlsAccountName, path, stream);
    }

## <a name="download-a-file"></a>Descarga de un archivo
El siguiente fragmento muestra un método `DownloadFile` que se utiliza para descargar un archivo de una cuenta de Data Lake Store.

    // Download file
    public static void DownloadFile(string srcPath, string destPath)
    {
        var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
        var fileStream = new FileStream(destPath, FileMode.Create);

        stream.CopyTo(fileStream);
        fileStream.Close();
        stream.Close();
    }

## <a name="next-steps"></a>Pasos siguientes
* [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
* [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Referencia de SDK de .NET de Data Lake Store](https://msdn.microsoft.com/library/mt581387.aspx)
* [Referencia de REST de Data Lake Store](https://msdn.microsoft.com/library/mt693424.aspx)




<!--HONumber=Nov16_HO2-->


