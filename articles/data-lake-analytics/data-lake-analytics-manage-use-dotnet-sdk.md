---
title: "Administración de Azure Data Lake Analytics con el SDK de .NET de Azure | Microsoft Docs"
description: "Aprenda a administrar trabajos, orígenes de datos y usuarios de Análisis de Data Lake. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 3ed1b4121e8e64b07abaeb1117f2b8a0cfd75406
ms.openlocfilehash: 7b2380e45c62684ed29fe819db7e254b968d55d0


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Administración de Azure Data Lake Analytics con el SDK de .NET para Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Obtenga información sobre cómo administrar cuentas, orígenes de datos, usuarios y trabajos de Azure Data Lake Analytics mediante el SDK de .NET para Azure. Para ver los temas de administración con otras herramientas, haga clic en el selector de pestañas de arriba.

**Requisitos previos**

Antes de comenzar este tutorial, debe cumplir los siguientes requisitos previos:

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="create-an-azure-resource-group"></a>Crear un grupo de recursos de Azure
Si aún no ha creado ninguno, debe tener un grupo de recursos de Azure para crear los componentes de Data Lake Analytics. El código siguiente muestra cómo crear un grupo de recursos:

    public static async Task<ResourceGroup> CreateResourceGroupAsync(
        ServiceClientCredentials credential,
        string groupName,
        string subscriptionId,
        string location)
    {

        Console.WriteLine("Creating the resource group...");
        var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
        var resourceGroup = new ResourceGroup { Location = location };
        return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
    }

Consulte [Grupos de recursos de Azure y Data Lake Analytics](## Azure Resource Groups and Data Lake Analytics) para más información.


## <a name="connect-to-azure-data-lake"></a>Conexión a Azure Data Lake
Necesita los siguientes paquetes NuGet:

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common
    Install-Package Microsoft.Azure.Common.Dependencies
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
    Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
    Install-Package Microsoft.WindowsAzure.Common
    Install-Package Microsoft.WindowsAzure.Common.Dependencies


El método Main del ejemplo de código siguiente muestra cómo conectarse a Azure e inicializar objetos de administración del cliente de Data Lake para una cuenta de Analytics y una cuenta de Store.

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.Azure.Management.DataLake.StoreUploader;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string _SubID = "<Specify your Azure subscription ID>"; 
            private const string _ClientID = "1950a258-227b-4e31-a9cf-717495945fc2"; // An ID made availble for developers
            private const string _resourceGroupName ="<Specify your resource group name>";
            private static string _location = "East US 2"; // Specify your location

            // Replace 'common' with user's Azure Active Directory tenant ID or domain name, if needed.
            private const string _Domain = "common"; 

            // Data Lake client management objects
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobsClient;

            private static void Main(string[] args)
            {

                // Call logon method
                var creds = AuthenticateAzure(_Domain, _ClientID);

                // Initialize Data Lake management client objects, using
                // your credentials (creds). Initialize others as needed.
                _adlsClient = new DataLakeStoreAccountManagementClient(creds);
                _adlsClient.SubscriptionId = _SubID;
                
                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = _SubID; 


                // Methods to create and manage Data Lake accounts and resources
                . . .

            }

            // Interactive logon
            public static ServiceClientCredentials AuthenticateAzure(string domainName, string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }
        }
    }

## <a name="data-lake-client-management-objects"></a>Objetos de administración del cliente de Data Lake
El SDK de Azure Data Lake incluye conjuntos de objetos de administración del cliente a partir de los cuales puede realizar la mayoría de la programación; dichos conjuntos se encuentran en estos dos espacios de nombres:
* Mirosoft.Azure.Management.DataLake.Analytics
* Microsot.Azure.Management.DataLake.Store

En la tabla siguiente se enumeran los objetos y sus variables usados en los ejemplos de este artículo.

| Objeto de administración del cliente                  | Variable del código         |
| ----------------------------------------- | --------------------- |
| DataLakeStoreAccountManagementClient      | _adlsClient           |
| DataLakeAnalyticsAccountManagementClient  | _adlaClient           |
| DataLakeStoreFileSystemManagementClient   | _adlsFileSystemClient |
| DataLakeAnalyticsCatalogManagementClient  | _adlaCatalogClient    |
| DataLakeAnalyticsJobManagementClient      | _adlaJobsClient       |

### <a name="data-lake-store-management-client-objects"></a>Objetos de administración del cliente de Data Lake Store:
* DataLakeStoreAccountManagementClient: se usa para crear y administrar almacenes de Data Lake.
* DataLakeFileSystemAccountManagementClient: se usa para tareas de sistema de archivos como crear carpetas y archivos, cargar y mostrar una lista de archivos, acceder a ACL y las credenciales, y agregar vínculos a Azure Storage Blobs.

Aunque puede crear vínculos a Azure Storage desde Data Lake, no puede acceder a su contenido. Para hacerlo, debe usar las API del SDK de Azure Storage. Sin embargo, puede ejecutar scripts de U-SQL en Azure Storage Blobs.

### <a name="data-lake-analytics-management-client-objects"></a>Objetos de administración del cliente de Data Lake Analytics:
* DataLakeAnaylyticsAccountManagementClient: se usa para crear y administrar cuentas de Data Lake Analytics.
* DataLakeAnalyticsCatalogManagementClient: se usa para configurar bases de datos de SQL, incluido el esquema de listas.
* DataLakeAnalyticsJobManagementClient: se usa para crear y administrar trabajos de U-SQL.

## <a name="create-accounts"></a>Creación de cuentas
Antes de ejecutar un trabajo de Análisis de Data Lake, debe tener una cuenta de Análisis de Data Lake. A diferencia de HDInsight de Azure, no se paga por una cuenta de Análisis cuando no está ejecutando un trabajo.  Solo se paga por el tiempo durante el que se ejecuta un trabajo.  Para obtener más información, consulte la página con [información general sobre Análisis con Azure Data Lake](data-lake-analytics-overview.md).

Además, cada cuenta de Data Lake Analytics requiere al menos una cuenta de Data Lake Store.
  
### <a name="create-a-data-lake-store-account"></a>Crear una cuenta de Almacén de Data Lake
El código siguiente muestra cómo crear una cuenta de Data Lake Store. Antes de utilizar el método Create, debe definir sus parámetros mediante la especificación de una ubicación.

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

### <a name="create-a-data-lake-analytics-account"></a>Creación de una cuenta de Análisis de Data Lake
El código siguiente muestra cómo crear una cuenta de Data Lake Analytics. El método Create del objeto DataLakeAnalyticsAccountManagementClient toma una colección de cuentas de Data Lake Store para uno de sus parámetros. Esta colección se debe rellenar con instancias de objetos DataLakeStoreAccountInfo. En este ejemplo, estos objetos DataLakeStoreAccountInfo se obtienen de un método auxiliar (AdlaFromAdlsStoreAccounts). Además, no todas las cuentas de Data Lake Store en una suscripción deben estar necesariamente en una sola cuenta de Data Lake analytics, por lo que este código comprueba los nombres en una lista aprobada.

        // create analytics account
        public void CreateAnalyticsAccount(string acctname)
        {
            IEnumerable<DataLakeStoreAccountInfo> dlaInfos = AdlaFromAdlsStoreAccounts();

            var dlInfo = new DataLakeAnalyticsAccount()
            {
                DefaultDataLakeStoreAccount = _adlsAccountName,
                Location = _location,
                DataLakeStoreAccounts = dlaInfos.ToList<DataLakeStoreAccountInfo>()
            };

            _adlaClient.Account.Create(_resourceGroupName, acctname, dlInfo);
        }

        // A helper method to collect Data Lake Store account information to create an  
        // an analytics account, and also validates accounts before including.
        public IEnumerable<DataLakeStoreAccountInfo> AdlaFromAdlsStoreAccounts()
        {
            List<DataLakeStoreAccount> adlsAccounts = _adlsClient.Account.List().ToList();

            // Create a collection for approved accounts
            List<DataLakeStoreAccount> approvedAccounts = new List<DataLakeStoreAccount>();

            foreach (DataLakeStoreAccount dlsa in adlsAccounts)
            {
                // The IsApprovedDataStore method (not shown) 
                // evaluates a Data Lake store name.
                if (IsApprovedDataStore(dlsa.Name))
                {
                    approvedAccounts.Add(dlsa);
                }
            }

            return approvedAccounts.Select(element => new DataLakeStoreAccountInfo(element.Name));
        }

## <a name="manage-accounts"></a>Administrar cuentas

### <a name="list-data-lake-store-and-analytic-accounts"></a>Lista de las cuentas de Data Lake Store y Analytics
El código siguiente muestra las cuentas de Data Lake Store en una suscripción. La lista de operaciones no siempre proporciona todas las propiedades de un objeto y, en ocasiones, es necesario realizar una operación Get en el objeto.
            
    var adlsAccounts = _adlsClient.Account.List().ToList();
    Console.WriteLine($"You have {adlsAccounts.Count} Data Lake Store accounts.");
    for (int i = 0; i < adlsAccounts.Count; i++)
    {
        Console.WriteLine($"\t{adlsAccounts[i].Name}");
    }

    var adlaAccounts = _adlaClient.Account.List().ToList();
    Console.WriteLine($"\nYou have {adlaAccounts.Count} Data Lake Analytic accounts.");
    for (int j = 0; j < adlaAccounts.Count; j++)
    {
        Console.WriteLine($"\t{adlaAccounts[j].Name}");
    }
        

        
### <a name="get-an-account"></a>Obtención de una cuenta
El código siguiente usa un objeto DataLakeAnalyticsAccountManagementClient para devolver una cuenta de Data Lake Analytics, si existe. 

    public DataLakeAnalyticsAccount GetDlaAccount(string strName)
    {
        DataLakeAnalyticsAccount dlaGet;
        if (_adlaClient.Account.Exists(_resourceGroupName, strName))
        {
            dlaGet = _adlaClient.Account.Get(_resourceGroupName, strName);
            Console.WriteLine($"{dlaGet.Name}\tCreated: {dlaGet.CreationTime}");
            return dlaGet;
        }
        else
        {
            return null;
        }

Puede utilizar DataLakeStoreAccountManagementClient (_adlsClient) de la misma manera para obtener una cuenta de Data Lake Store.        
### <a name="delete-an-account"></a>Eliminación de una cuenta
El código siguiente elimina una cuenta de Data Lake Analytics. 

    public void DeleteAnalyticsAccount(string strName)
    {
        if (_adlaClient.Account.Exists(_resourceGroupName, strName))
        {
            _adlaClient.Account.Delete(_resourceGroupName, strName);
            Console.WriteLine($"{strName} Deleted");
        }
        else
        {
            Console.WriteLine($"{strName} does not exist.");
        }

    }

También puede eliminar una cuenta de Data Lake Store de la misma manera con un objeto DataLakeStoreAccountManagementClient.

### <a name="get-the-default-data-lake-store-account"></a>Obtención de la cuenta predeterminada de Data Lake Store
Cada cuenta de Data Lake Analytics requiere una cuenta de Data Lake Store predeterminada. Utilice este código para determinar la cuenta de Store predeterminada para una cuenta de Analytics.

    public void GetDefaultDLStoreAccount(string DLAaccountName)
    {
        if (_adlaClient.Account.Exists(_resourceGroupName, DLAaccountName))
        {
            DataLakeAnalyticsAccount dlaGet = _adlaClient.Account.Get(_resourceGroupName, DLAaccountName);
            Console.WriteLine($"{dlaGet.Name} default DL store account: {dlaGet.DefaultDataLakeStoreAccount}");
        }
    }

## <a name="manage-data-sources"></a>Administración de orígenes de datos
Actualmente, Análisis de Data Lake admite los siguientes orígenes de datos:

* [Almacén de Azure Data Lake](../data-lake-store/data-lake-store-overview.md)
* [Almacenamiento de Azure](../storage/storage-introduction.md)

Cuando crea una cuenta de Análisis, debe designar una cuenta de Almacén de Azure Data Lake para que sea la cuenta de almacenamiento predeterminada. La cuenta predeterminada de Almacén de Data Lake sirve para almacenar los registros de auditoría y de metadatos de trabajos. Una vez creada la cuenta de Analytics, puede agregar más cuentas de Data Lake Store y vínculos a cuentas de Azure Storage. 

### <a name="include-a-link-to-azure-storage-in-data-lake"></a>Inclusión de un vínculo a Azure Storage en Data Lake
Puede crear vínculos en el entorno de Data Lake para blogs de Azure Storage. 

    string storageKey = "<paste the key value here>";

    AddStorageAccountParameters addParams = new AddStorageAccountParameters(storageKey);            
    _adlaClient.StorageAccounts.Add(_resourceGroupName, _adlaAccountName, "<Azure Storage Account Name>", addParams);

### <a name="list-data-lake-data-sources"></a>Lista de orígenes de datos de Data Lake
El código siguiente enumera las cuentas de Data Lake Store y las de Data Lake Storage para Azure Storage de una cuenta de Data Lake Analytics especificada.

    var sAccnts = _adlaClient.StorageAccounts.ListByAccount(_resourceGroupName, acctName);

    if (sAccnts != null)
    {
        Console.WriteLine("Storage accounts:");
        foreach (var a in sAccnts)
        {
            Console.WriteLine($"\t{a.Name}");
        }
    }

    var stores = _adlsClient.Account.List();
    if (stores != null)
    {
        Console.WriteLine("\nData stores:");
        foreach (var s in stores)
        {
            Console.WriteLine($"\t{s.Name}");
        }
    }

### <a name="upload-a-file-to-a-data-lake-store-account"></a>Carga de un archivo en una cuenta de Data Lake Store
El código siguiente usa un objeto DataLakeStoreFileSystemManagementClient para cargar un archivo local en una cuenta de Data Lake Store.

    bool force = true;
    string adlsAccnt = "Accounting";
    string srcFilePath = @"c:\DataLakeTemp\localData.csv";
    string dstFilePath = "/Reports/FY2016/2016data.csv";
    var parameters = new UploadParameters(srcFilePath, dstFilePath, adlsAccnt, isOverwrite: force);
    var frontend = new DataLakeStoreFrontEndAdapter(adlsAccnt, _adlsFileSystemClient);
    var uploader = new DataLakeStoreUploader(parameters, frontend);
    uploader.Execute();

### <a name="create-a-file-in-a-data-lake-store-account"></a>Creación de un archivo en una cuenta de Data Lake Store
Además de cargar archivos, puede crear archivos fácilmente mediante programación en su cuenta de Data Lake Store para el análisis. El código siguiente escribe los primeros cuatro valores de 100 matrices de bytes aleatorios en el archivo .csv.

        MemoryStream azMem = new MemoryStream();
        StreamWriter sw = new StreamWriter(azMem, UTF8Encoding.UTF8);

        for (int i = 0; i < 100; i++)
        {
            byte[] gA = Guid.NewGuid().ToByteArray();
            string dataLine = string.Format($"{gA[0].ToString()},{gA[1].ToString()},{gA[2].ToString()},{gA[3].ToString()},{gA[4].ToString()}");
            sw.WriteLine(dataLine);
        }
        sw.Flush();
        azMem.Position = 0;

        _adlsFileSystemClient.FileSystem.Create(adlsAccoutName, "/Samples/Output/randombytes.csv", azMem);

        sw.Dispose();
        azMem.Dispose();

### <a name="copy-files-from-a-data-lake-store-account"></a>Copia de archivos de una cuenta de Data Lake Store
En el siguiente código se muestran operaciones del sistema de archivos con un objeto DataLakeFileSystemAccountManagementClient. Copia los archivos de hoja de cálculo (.csv) del directorio Samples/Data/AmbulanceData en un directorio local en su equipo.

    // This method takes the name of a Data Lake Store account,
    // and the the path to a directory in the account. In this

    public void CopyCSVFiles(string accnt, string fPath)
    {
        try
        {
            if (_adlsFileSystemClient.FileSystem.PathExists(accnt,fPath))
            {
                var fStatus = _adlsFileSystemClient.FileSystem.ListFileStatus(accnt, fPath);
                foreach (var fs in fStatus.FileStatuses.FileStatus)
                {
                    string localF = string.Empty;
                    if (fs.Type == Microsoft.Azure.Management.DataLake.Store.Models.FileType.FILE &&
                        fs.PathSuffix.Contains("csv"))
                    {
                        Stream fStream = _adlsFileSystemClient.FileSystem.Open(accnt, fPath + "/" + fs.PathSuffix);
                        localF = @"c:\DataLakeTemp\" + fs.PathSuffix;
                        FileStream localStream = new FileStream(localF, FileMode.Create);
                        fStream.CopyTo(localStream);

                    }
                    Console.WriteLine($"Copied {localF}.");
                }
            }
            else
            {
                Console.WriteLine($"File path {fPath} does not exist.");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }

### <a name="list-azure-storage-containers"></a>Lista de contenedores de Azure Storage
El código siguiente enumera los contenedores de una cuenta de Azure Storage especificada.

    string DLAName = "<specify Data Lake Analytics account name>";
    string azStorageName = "<specify Azure Storage account name>";
    var containers = _adlaClient.StorageAccounts.ListStorageContainers(_resourceGroupName, DLAName, azStorageName);
    foreach (var c in containers)
    {
       Console.WriteLine(c.Name);
    }

### <a name="verify-azure-storage-account-paths"></a>Verificación de las rutas de acceso a la cuenta de Azure Storage
El código siguiente comprueba si hay una cuenta de Azure Storage (storageAccntName) en una cuenta de Data Lake Analytics (analyticsAccountName), y si hay un contenedor (containerName) en la cuenta de Azure Storage. 

    bool accountExists = _adlaClient.Account.StorageAccountExists(_resourceGroupName, analyticsAccountName, storageAccntName));
    bool containerExists = _adlaClient.Account.StorageContainerExists(_resourceGroupName, analyticsAccountName, storageAccntName, containerName));

## <a name="manage-catalog-and-jobs"></a>Administración del catálogo y los trabajos
El objeto DataLakeAnalyticsCatalogManagementClient proporciona métodos para administrar la instancia de SQL Database proporcionada para cada Azure Data Lake Store. DataLakeAnalyticsJobManagementClient proporciona métodos para enviar y administrar trabajos ejecutados en la base de datos con scripts de U-SQL.

### <a name="list-databases-and-schemas"></a>Lista de bases de datos y esquemas
Entre las diversas cosas que puede incluir en la lista, las más comunes son las bases de datos y su esquema. El código siguiente obtiene una colección de bases de datos y, a continuación, enumera el esquema de cada base de datos.

    private void ListCatalogItems(string dlaAccountName)
    {
        var databases = _adlaCatalogClient.Catalog.ListDatabases(dlaAccountName);
        foreach (var db in databases)
        {
            Console.WriteLine($"Database: {db.Name}");
            Console.WriteLine(" - Schemas:");
            var schemas = _adlaCatalogClient.Catalog.ListSchemas(dlaAccountName, db.Name);
            foreach (var schm in schemas)
            {
                Console.WriteLine($"\t{schm.Name}");
            }
        }
    }

Ejecutado en la base de datos maestra de manera predeterminada, la salida de este ejemplo es la siguiente:

    Database: master
    - Schemas:
            dbo
            INFORMATION_SCHEMA
            sys
            usql

### <a name="list-table-columns"></a>Lista de columnas de la tabla
El código siguiente muestra cómo acceder a la base de datos con un cliente de administración del catálogo de Data Lake Analytics para mostrar las columnas de una tabla especificada.

    var tbl = _adlaCatalogClient.Catalog.GetTable(_adlaAnalyticsAccountTest, "master", "dbo", "MyTableName");
    IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

    foreach (USqlTableColumn utc in columns)
    {
        string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
        Stream scriptStrm = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
        string scriptTxt = string.Empty;
        using (StreamReader sr = new StreamReader(scriptStrm))
        {
            scriptTxt = sr.ReadToEnd();
        }

        var jobName = "SR_Wikipedia";
        var jobId = Guid.NewGuid();
        var properties = new USqlJobProperties(scriptTxt);
        var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
        var jobInfo = _adlaJobsClient.Job.Create(_adlaAnalyticsAccountTest, jobId, parameters);
        Console.WriteLine($"Job {jobName} submitted.");

    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }


### <a name="list-failed-jobs"></a>Lista de trabajos con errores
El código siguiente muestra información acerca de trabajos con errores.

    var jobs = _adlaJobsClient.Job.List(_adlaAnalyticsAccountName);

    foreach (var j in jobs)
    {
        if (j.Result == JobResult.Failed)
        {
            Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
        }
    }
### <a name="reference-azure-storage-in-u-sql-scripts"></a>Referencia de Azure Storage en scripts de U-SQL
El código siguiente es el principio de un script de U-SQL. En este script se indica que se lean los datos de un archivo en una cuenta de Data Lake Store: "/Samples/Data/SearchLog.tsv".

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

Para leer datos de un blob en una cuenta de Azure Storage vinculada, debe utilizar la dirección URL completa del blob con el formato siguiente:

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/<path to source>

Por ejemplo, si un archivo de origen (SearchLog.tsv) se almacena en un contenedor de blobs denominado "samples" en la cuenta de almacenamiento "contso_33", la ruta de acceso de la instrucción FROM sería:

    FROM: "wasb://samples@constoso_33.blob.core.windows.net/SearchLog.tsv"

## <a name="azure-resource-groups-and-data-lake-analytics"></a>Grupos de recursos de Azure y Data Lake Analytics
Las aplicaciones normalmente se componen de muchos componentes, por ejemplo, una aplicación web, base de datos, servidor de base de datos, almacenamiento y servicios de terceros. Azure Resource Manager le permite trabajar con los recursos de la aplicación como un grupo al que se hace referencia como un Azure Resource Group. Puede implementar, actualizar, supervisar o eliminar todos los recursos de la aplicación en una operación única y coordinada. Para realizar la implementación se usa una plantilla, que puede funcionar en distintos entornos, como producción, pruebas y ensayo. Puede aclarar la facturación de la organización consultando los costes acumulados de todo el grupo. Para obtener más información, consulte [Información general del Administrador de recursos de Azure](../azure-resource-manager/resource-group-overview.md). 

Un servicio Análisis de Data Lake puede incluir los siguientes componentes:

* Cuenta de Análisis de Azure Data Lake
* Cuenta predeterminada y necesaria de Almacén de Azure Data Lake
* Una o varias cuentas de Azure Data Lake Analytics
* Una o varias cuentas de Azure Data Lake Store; es necesaria al menos una
* Cuentas adicionales de Azure Data Lake Storage vinculadas
* Cuentas adicionales de Almacenamiento de Azure

Puede crear todos estos componentes en un grupo de Resource Manager para que sea más fáciles de administrar.

![Cuenta y almacenamiento de Análisis de Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

La cuenta de Análisis de Data Lake y las cuentas de almacenamiento dependientes deben ubicarse en el mismo centro de datos de Azure.
Sin embargo, el grupo de Resource Manager se puede ubicar en un centro de datos distinto.  

## <a name="see-also"></a>Consulte también
* [Información general de Análisis de Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Introducción a Data Lake Analytics mediante el portal de Azure](data-lake-analytics-get-started-portal.md)
* [Administración de Azure Data Lake Analytics con Azure Portal](data-lake-analytics-manage-use-portal.md)
* [Supervisión y solución de problemas de trabajos de Azure Data Lake Analytics con Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)



<!--HONumber=Feb17_HO1-->


