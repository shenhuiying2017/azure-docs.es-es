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
ms.date: 03/3/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: bed6fa355f3b32bb53aee002e34ca61f2ea2aa5b
ms.lasthandoff: 03/06/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Administración de Azure Data Lake Analytics con el SDK de .NET para Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Obtenga información sobre cómo administrar cuentas, orígenes de datos, usuarios y trabajos de Azure Data Lake Analytics mediante el SDK de .NET para Azure. Para ver los temas de administración con otras herramientas, haga clic en el selector de pestañas de arriba.

## <a name="prerequisites"></a>Requisitos previos

* **Visual Studio 2015, Visual Studio 2013 Update 4 o Visual Studio 2012 con Visual C++ instalado**.
* **SDK de Microsoft Azure para .NET versión 2.5 o posterior**.  Instálelo usando el [instalador de plataforma web](http://www.microsoft.com/web/downloads/platform.aspx).
* **Paquetes de NuGet necesarios**

### <a name="install-nuget-packages"></a>Instalar paquetes de NuGet
   
   1. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el nombre del proyecto y seleccione **Administrar paquetes de NuGet**.
   2. En la pestaña **Administrador de paquetes NuGet**, asegúrese de que la opción **Origen del paquete** esté establecida en **nuget.org** y que esté activada la casilla **Incluir versión previa**.

   3. Busque e instale los siguientes paquetes NuGet:

    - Microsoft.Rest.ClientRuntime.Azure.Authentication: este tutorial usa V2.2.12
    - Microsoft.Azure.Management.DataLake.Analytics: este tutorial usa la versión preliminar V2.1.0
    - Microsoft.Azure.Management.DataLake.Store: este tutorial usa la versión preliminar V2.1.0

   4. Cierre el **Administrador de paquetes Nuget**.

## <a name="client-management-objects"></a>Objetos de administración de cliente
Las API de Azure Data Lake Analytics y Azure Data Lake Store incluyen conjuntos de objetos de administración de cliente a partir de los cuales se puede realizar la mayoría de la programación. Estos objetos están en estos dos espacios de nombres:
* Microsoft.Azure.Management.DataLake.Analytics
* Microsoft.Azure.Management.DataLake.Store

En la tabla siguiente se muestra los objetos de administración de cliente, junto con las variables que se usan en los ejemplos de código a lo largo de este artículo.

| Objeto de administración del cliente                  | Variable del código        |
| ----------------------------------------- | -------------------- |
| DataLakeStoreAccountManagementClient      | adlsClient           |
| DataLakeAnalyticsAccountManagementClient  | adlaClient           |
| DataLakeStoreFileSystemManagementClient   | adlsFileSystemClient |
| DataLakeAnalyticsCatalogManagementClient  | adlaCatalogClient    |
| DataLakeAnalyticsJobManagementClient      | adlaJobClient        |

### <a name="data-lake-store-management-client-objects"></a>Objetos de administración del cliente de Data Lake Store:
* DataLakeStoreAccountManagementClient: se usa para crear y administrar cuentas de Data Lake Store.
* DataLakeFileSystemAccountManagementClient: se usa para tareas de sistema de archivos como crear carpetas y archivos, cargar y mostrar una lista de archivos, acceder a ACL y las credenciales, y agregar vínculos a Azure Storage Blobs.

Aunque puede crear vínculos a Azure Storage desde Data Lake, no puede acceder a su contenido. Para hacerlo, debe usar las API del SDK de Azure Storage. Sin embargo, puede ejecutar scripts de U-SQL en Azure Storage Blobs.

### <a name="data-lake-analytics-management-client-objects"></a>Objetos de administración del cliente de Data Lake Analytics:
* DataLakeAnalyticsAccountManagementClient: se usa para crear y administrar cuentas de Data Lake Analytics.
* DataLakeAnalyticsCatalogManagementClient: se usa para explorar los elementos de catálogo de Data Lake Analytics.
* DataLakeAnalyticsJobManagementClient: para enviar y administrar trabajos en Data Lake Analytics.

### <a name="example"></a>Ejemplo

Inicialice objetos de administración de cliente con las credenciales que se obtienen mediante su método de autenticación preferido, descrito a continuación en este artículo. 

    // Only the Data Lake Analytics and Data Lake Store  
    // objects need a subscription ID.
    adlsClient = new DataLakeStoreAccountManagementClient(creds);
    adlsClient.SubscriptionId = <Subscription-ID>;

    adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
    adlaClient.SubscriptionId = <Subscription-ID>;

    adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);
    adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(creds);
    adlaJobClient = new DataLakeAnalyticsJobManagementClient(creds);


    // Methods to create and manage Data Lake Analytics
    . . .

## <a name="authenticate-and-connect-to-azure-data-lake-analytics"></a>Autenticarse y conectarse a Azure Data Lake Analytics
Tiene varias opciones para registrarse en Azure Data Lake Analytics.

### <a name="interactive-with-provided-credentials"></a>Interactivo con credenciales proporcionadas
El fragmento de código siguiente muestra la autenticación más sencilla con las credenciales proporcionadas por el usuario, como un nombre de usuario y una contraseña o un número pin.

    // User login via interactive popup
    // Use the client ID of an existing AAD "native nlient" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Tenant ID>"; // Replace this string with the user's Azure Active Directory tenant ID.
    var clientId = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(_tenantId, activeDirectoryClientSettings).Result;

Se recomienda crear su propia aplicación y entidad de servicio en el inquilino de Azure Active Directory y después usar el identificador de cliente para esa aplicación, en lugar del identificador de ejemplo que se usa aquí.

### <a name="non-interactive-with-a-client-secret"></a>No interactivo con un secreto de cliente
Se puede usar el siguiente fragmento de código para autenticar la aplicación de forma no interactiva, mediante el secreto o la clave del cliente para una entidad de servicio o aplicación. Use esta opción de autenticación con una [aplicación web de Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md) ya existente.

    // Service principal / application authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Azure tenant ID>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(tenantId, clientCredential).Result;

### <a name="non-interactive-with-a-service-principal"></a>No interactivo con una entidad de servicio
Como alternativa, el siguiente fragmento de código puede utilizarse para autenticar la aplicación de forma no interactiva, para lo que se usa el secreto de cliente o la clave para una identidad de servicio o aplicación. Use esta opción de autenticación con una [aplicación web de Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md) ya existente.

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Azure tenant ID>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(tenantId, clientAssertionCertificate).Result;

## <a name="create-accounts"></a>Creación de cuentas
Antes de ejecutar un trabajo de Análisis de Data Lake, debe tener una cuenta de Análisis de Data Lake. Además, cada cuenta de Data Lake Analytics requiere al menos una cuenta de Data Lake Store. Para más información, vea [Información general de Azure Data Lake Analytics](data-lake-analytics-overview.md)

### <a name="create-an-azure-resource-group"></a>Crear un grupo de recursos de Azure
Si aún no ha creado ninguno, debe tener un grupo de recursos de Azure para crear los componentes de Data Lake Analytics. Necesitará las credenciales de autenticación, el identificador de la suscripción y una ubicación. El código siguiente muestra cómo crear un grupo de recursos:

    string rgName == "<value>"; // specify name for the new resrouce group
    var resourceManagementClient = new ResourceManagementClient(credential) { SubscriptionId = subscriptionId };
    var resourceGroup = new ResourceGroup { Location = location };
    resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rgName);

Para más información, vea [Grupos de recursos de Azure y Data Lake Analytics](#Azure-Resource-Groups-and-Data-Lake-Analytics).


### <a name="create-a-data-lake-store-account"></a>Crear una cuenta de Almacén de Data Lake
El código siguiente muestra cómo crear una cuenta de Data Lake Store. Antes de utilizar el método Create, debe definir sus parámetros mediante la especificación de una ubicación.

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

### <a name="create-a-data-lake-analytics-account"></a>Creación de una cuenta de Análisis de Data Lake
El código siguiente muestra cómo crear una cuenta de Data Lake Analytics mediante el método asincrónico. El método CreateAsync toma una colección de cuentas de Data Lake Store como uno de sus parámetros. Esta colección se debe rellenar con instancias de objetos DataLakeStoreAccountInfo. En este ejemplo, estos objetos DataLakeStoreAccountInfo se obtienen de un método auxiliar (AdlaFromAdlsStoreAccounts).

Para cualquier cuenta de Data Lake Analytics, solo se deben incluir las cuentas de Data Lake Store que se necesitan para realizar los análisis necesarios. Una de estas cuentas de Data Lake Store debe ser la cuenta predeterminada de Data Lake Store.

    try
    {
        var adlaAccount = new DataLakeAnalyticsAccount()
        {
            DefaultDataLakeStoreAccount = “Accounting”,
            Location = _location,
            DataLakeStoreAccounts = new DataLakeStoreAccountInfo[]{
                new DataLakeStoreAccountInfo(“Expenditures”),
                new DataLakeStoreAccountInfo(“Accounting”)
            }
        };
        adlaClient.Account.Create(_resourceGroupName, newAccountName, adlaAccount);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }

## <a name="manage-accounts"></a>Administrar cuentas

### <a name="list-data-lake-store-and-data-lake-analytics-accounts"></a>Enumerar las cuentas de Data Lake Store y Data Lake Analytics
El código siguiente muestra las cuentas de Data Lake Store de una suscripción. La lista de operaciones no siempre proporciona todas las propiedades de un objeto y, en ocasiones, es necesario realizar una operación Get en el objeto.

    var adlsAccounts = adlsClient.Account.List().ToList();
    foreach (var adls in adlsAccounts)
    {
        Console.WriteLine($"\t{adls.Name});

    }

    var adlaAccounts = adlaClient.Account.List().ToList();
    for (var adla in AdlaAccounts)
    {
        Console.WriteLine($"\t{adla.Name}");
    }



### <a name="get-an-account"></a>Obtención de una cuenta
El código siguiente usa un objeto DataLakeAnalyticsAccountManagementClient para obtener una cuenta de Data Lake Analytics. Primero se realiza una comprobación para ver si la cuenta existe.

    DataLakeAnalyticsAccount adlaGet;
    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        adlaGet = adlaClient.Account.Get(_resourceGroupName, accountName);
        Console.WriteLine($"{adlaGet.Name}\tCreated: {adlaGet.CreationTime}");
    }

También se puede usar DataLakeStoreAccountManagementClient (adlsClient) de la misma manera para obtener una cuenta de Data Lake Store.

### <a name="delete-an-account"></a>Eliminación de una cuenta
El código siguiente elimina una cuenta de Data Lake Analytics si existe.

    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        adlaClient.Account.Delete(_resourceGroupName, accountName);
        Console.WriteLine($"{accountName} Deleted");
    }
    else
    {
        Console.WriteLine($"{accountName} does not exist.");
    }

También puede eliminar una cuenta de Data Lake Store de la misma manera con un objeto DataLakeStoreAccountManagementClient.

### <a name="get-the-default-data-lake-store-account"></a>Obtención de la cuenta predeterminada de Data Lake Store
Cada cuenta de Data Lake Analytics requiere una cuenta de Data Lake Store predeterminada. Utilice este código para determinar la cuenta de Store predeterminada para una cuenta de Analytics.

    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        DataLakeAnalyticsAccount adlaGet = adlaClient.Account.Get(_resourceGroupName, accountName);
        Console.WriteLine($"{adlaGet.Name} default DL store account: {adlaGet.DefaultDataLakeStoreAccount}");
    }


## <a name="manage-data-sources"></a>Administración de orígenes de datos
Actualmente, Análisis de Data Lake admite los siguientes orígenes de datos:

* [Almacén de Azure Data Lake](../data-lake-store/data-lake-store-overview.md)
* [Cuenta de Azure Storage](../storage/storage-introduction.md)

Cuando se crea una cuenta de Analytics, se debe designar una cuenta de Azure Data Lake Store para que sea la cuenta predeterminada. La cuenta predeterminada de Almacén de Data Lake sirve para almacenar los registros de auditoría y de metadatos de trabajos. Una vez creada la cuenta de Analytics, se pueden agregar más cuentas de Data Lake Store y vínculos a cuentas de Azure Storage (blobs).

### <a name="link-to-an-azure-storage-account-from-a-data-lake-analytics-account"></a>Vínculo a una cuenta de Azure Storage desde una cuenta de Data Lake Analytics
Se pueden crear vínculos a cuentas de Azure Storage.

    AddStorageAccountParameters addParams = new AddStorageAccountParameters(<storage key value>);            
    adlaClient.StorageAccounts.Add(_resourceGroupName, _adlaAccountName, "<Azure Storage Account Name>", addParams);

### <a name="list-data-lake-store-data-sources"></a>Lista de orígenes de datos de Data Lake Store
El código siguiente enumera las cuentas de Data Lake Store y las de Azure Storage que se usan para una cuenta de Data Lake Analytics especificada.

    var sAccnts = adlaClient.StorageAccounts.ListByAccount(_resourceGroupName, acctName);

    if (sAccnts != null)
    {
        Console.WriteLine("Azure Storage accounts:");
        foreach (var a in sAccnts)
        {
            Console.WriteLine($"\t{a.Name}");
        }
    }

    var stores = adlsClient.Account.List();
    if (stores != null)
    {
        Console.WriteLine("\nData stores:");
        foreach (var s in stores)
        {
            Console.WriteLine($"\t{s.Name}");
        }
    }

### <a name="upload-and-download-folders-and-files"></a>Cargar y descargar archivos y carpetas
El objeto de administración de cliente del sistema de archivos de Data Lake Store se puede usar para cargar y descargar archivos o carpetas individuales desde Azure al equipo local, usando los métodos siguientes:

- UploadFolder
- UploadFile
- DownloadFolder
- DownloadFile

El primer parámetro de estos métodos es el nombre de la cuenta de Data Lake Store, seguido de parámetros para la ruta de acceso de origen y la ruta de acceso de destino.

En el ejemplo siguiente se muestra cómo descargar una carpeta en Data Lake Store.


    try
    {
        if (adlsFileSystemClient.FileSystem.PathExists(account, sourcePath))
        {
            adlsFileSystemClient.FileSystem.DownloadFolder(account, sourcePath, destinationPath);
        }
        else
        {
            Console.WriteLine("Path does not exist");
        }
    }
    catch (IOException ioex)
    {
        Console.WriteLine(ioex.Message);
    }


### <a name="create-a-file-in-a-data-lake-store-account"></a>Crear un archivo en una cuenta de Data Lake Store
Se pueden usar operaciones de E/S de .NET Framework para crear contenido para un archivo en un Data Lake Store. El código siguiente escribe los primeros cuatro valores de 100 matrices de bytes aleatorios en el archivo .csv.

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

    adlsFileSystemClient.FileSystem.Create(adlsAccoutName, "/Samples/Output/randombytes.csv", azMem);

    sw.Dispose();
    azMem.Dispose();

### <a name="list-blob-containers-of-an-azure-storage-account"></a>Lista de contenedores de blob de una cuenta de Azure Storage
El código siguiente enumera los contenedores de una cuenta de Azure Storage especificada.

    string ADLAName = "<specify Data Lake Analytics account name>";
    string azStorageName = "<specify Azure Storage account name>";
    var containers = adlaClient.StorageAccounts.ListStorageContainers(_resourceGroupName, ADLAName, azStorageName);
    foreach (var c in containers)
    {
       Console.WriteLine(c.Name);
    }

### <a name="verify-azure-storage-account-paths"></a>Verificación de las rutas de acceso a la cuenta de Azure Storage
El código siguiente comprueba si hay una cuenta de Azure Storage (storageAccntName) en una cuenta de Data Lake Analytics (analyticsAccountName), y si hay un contenedor (containerName) en la cuenta de Azure Storage.

    bool accountExists = adlaClient.Account.StorageAccountExists(_resourceGroupName, analyticsAccountName, storageAccntName));
    bool containerExists = adlaClient.Account.StorageContainerExists(_resourceGroupName, analyticsAccountName, storageAccntName, containerName));

## <a name="manage-catalog-and-jobs"></a>Administración del catálogo y los trabajos
El objeto DataLakeAnalyticsCatalogManagementClient proporciona métodos para administrar la instancia de SQL Database proporcionada para cada Azure Data Lake Store. DataLakeAnalyticsJobManagementClient proporciona métodos para enviar y administrar trabajos ejecutados en la base de datos con scripts de U-SQL.

### <a name="list-databases-and-schemas"></a>Lista de bases de datos y esquemas
Entre las diversas cosas que se pueden incluir en la lista, las más comunes son las bases de datos y su esquema. El código siguiente obtiene una colección de bases de datos y, a continuación, enumera el esquema de cada base de datos.

    var databases = adlaCatalogClient.Catalog.ListDatabases(adlaAccountName);
    foreach (var db in databases)
    {
        Console.WriteLine($"Database: {db.Name}");
        Console.WriteLine(" - Schemas:");
        var schemas = adlaCatalogClient.Catalog.ListSchemas(dlaAccountName, db.Name);
        foreach (var schm in schemas)
        {
            Console.WriteLine($"\t{schm.Name}");
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

    var tbl = adlaCatalogClient.Catalog.GetTable(_adlaAnalyticsAccountTest, "master", "dbo", "MyTableName");
    IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

    foreach (USqlTableColumn utc in columns)
    {
        string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
        Stream scriptStrm = adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
        string scriptTxt = string.Empty;
        using (StreamReader sr = new StreamReader(scriptStrm))
        {
            scriptTxt = sr.ReadToEnd();
        }

        var jobName = "SR_Wikipedia";
        var jobId = Guid.NewGuid();
        var properties = new USqlJobProperties(scriptTxt);
        var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
        var jobInfo = adlaJobClient.Job.Create(_adlaAnalyticsAccountTest, jobId, parameters);
        Console.WriteLine($"Job {jobName} submitted.");

    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }


### <a name="list-failed-jobs"></a>Lista de trabajos con errores
El código siguiente muestra información acerca de trabajos con errores.

    var jobs = adlaJobClient.Job.List(adlaClient,
        new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" });
    foreach (var j in jobs)
    {
        Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
    }


## <a name="see-also"></a>Consulte también
* [Información general de Análisis de Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Introducción a Data Lake Analytics mediante el portal de Azure](data-lake-analytics-get-started-portal.md)
* [Administración de Azure Data Lake Analytics con Azure Portal](data-lake-analytics-manage-use-portal.md)
* [Supervisión y solución de problemas de trabajos de Azure Data Lake Analytics con Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

