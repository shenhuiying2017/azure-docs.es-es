---
title: 'Tutorial: Uso de la biblioteca de cliente de Azure Batch para .NET | Microsoft Docs'
description: "Aprenda los conceptos básicos de Azure Batch y cree una solución sencilla mediante .NET."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
ms.assetid: 76cb9807-cbc1-405a-8136-d1e53e66e82b
ms.service: batch
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 06/28/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 83f751c6b5e44705509804e6872bb16d7c2e1d18
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="get-started-building-solutions-with-the-batch-client-library-for-net"></a>Introducción a la creación de soluciones con la biblioteca de cliente de Batch para .NET

> [!div class="op_single_selector"]
> * [.NET](batch-dotnet-get-started.md)
> * [Python](batch-python-tutorial.md)
> * [Node.js](batch-nodejs-get-started.md)
>
>

En este artículo, se explican los aspectos básicos de [Azure Batch][azure_batch] y de la biblioteca [.NET de Batch][net_api] a medida que se analiza paso a paso un ejemplo de aplicación escrito en C#. Examinaremos la forma en que esta aplicación de ejemplo aprovecha el servicio Batch para procesar una carga de trabajo paralela en la nube y cómo interactúa con [Azure Storage](../storage/common/storage-introduction.md) para almacenar provisionalmente archivos y recuperarlos. Aprenderá un flujo de trabajo de la aplicación de Batch habitual y obtenga un conocimiento básico de los componentes principales de Batch, como trabajos, tareas, grupos y nodos de proceso.

![Flujo de trabajo de soluciones de Batch (básico)][11]<br/>

## <a name="prerequisites"></a>Requisitos previos
En este artículo se asume que tiene conocimientos prácticos de C# y Visual Studio. También se asume que puede cumplir los requisitos para la creación de cuentas que se especifican a continuación en Azure y los servicios Batch y Storage.

### <a name="accounts"></a>Cuentas
* **Cuenta de Azure**: si aún no tiene ninguna suscripción a Azure, [cree una cuenta gratuita de Azure][azure_free_account].
* **Cuenta de Batch**: una vez que tenga una suscripción a Azure, [cree una cuenta de Azure Batch](batch-account-create-portal.md).
* **Cuenta de almacenamiento**: consulte la sección [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account) del artículo [Acerca de las cuentas de Azure Storage](../storage/common/storage-create-storage-account.md).

> [!IMPORTANT]
> Actualmente, Batch *solo* admite el tipo de cuenta de almacenamiento **de uso general**, tal y como se describe en el paso 5 de la sección [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account) del artículo [Acerca de las cuentas de almacenamiento de Azure](../storage/common/storage-create-storage-account.md).
>
>

### <a name="visual-studio"></a>Visual Studio
Debe tener **Visual Studio 2015 o superior** para compilar el proyecto de ejemplo. Puede encontrar versiones de prueba y de evaluación gratuita de Visual Studio en la [información general de los productos de Visual Studio][visual_studio].

### <a name="dotnettutorial-code-sample"></a>*DotNetTutorial*
El ejemplo [DotNetTutorial][github_dotnettutorial] es uno de los muchos ejemplos de código de Batch que se encuentran en el repositorio [azure-batch-samples][github_samples] de GitHub. Para descargar todos los ejemplos, haga clic en **Clone or download > Download ZIP** (Clonar o descargar > Descargar ZIP) en la página principal del repositorio o haga clic en el vínculo de descarga directa [azure-batch-samples-master.zip][github_samples_zip]. Una vez extraído el contenido del archivo ZIP, puede encontrar la solución en la carpeta siguiente:

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### <a name="batchlabs-optional"></a>BatchLabs (opcional)
[BatchLabs][github_batchlabs] es una herramienta de cliente independiente, completa y gratuita que puede ayudarle a crear, depurar y supervisar aplicaciones de Azure Batch. Aunque esta utilidad no es necesaria para completar este tutorial, puede resultar útil para desarrollar y depurar las soluciones de Batch.

## <a name="dotnettutorial-sample-project-overview"></a>Información general del proyecto de ejemplo DotNetTutorial
El código de ejemplo *DotNetTutorial* es una solución de Visual Studio que consta de dos proyectos: **DotNetTutorial** y **TaskApplication**.

* **DotNetTutorial** es la aplicación cliente que interactúa con los servicios Batch y Storage para ejecutar una carga de trabajo paralela en los nodos de proceso (máquinas virtuales). DotNetTutorial se ejecuta en la estación de trabajo local.
* **TaskApplication** es el programa que se ejecuta en los nodos de proceso de Azure para realizar el trabajo real. En el ejemplo, `TaskApplication.exe` analiza el texto de un archivo descargado de Azure Storage (el archivo de entrada). Luego genera un archivo de texto (el archivo de salida) que contiene una lista de las tres palabras que más veces aparecen en el archivo de entrada. Después de crear el archivo de salida, TaskApplication lo carga en Azure Storage. Esto hace que esté disponible para la aplicación cliente para su descarga. El proyecto TaskApplication se ejecuta en paralelo en varios nodos de ejecución en el servicio Batch.

El siguiente diagrama ilustra las operaciones principales que realiza la aplicación cliente, *DotNetTutorial*, y la aplicación que ejecutan las tareas, *TaskApplication*. Este flujo de trabajo básico es típico de muchas soluciones de proceso que se crean con Batch. Aunque no muestra todas las características disponibles en el servicio Batch , casi todos los escenarios de Batch incluyen partes de este flujo de trabajo.

![Flujo de trabajo de ejemplo de Batch][8]<br/>

[**Paso 1.**](#step-1-create-storage-containers) Crear **contenedores** en Azure Blob Storage.<br/>
[**Paso 2.**](#step-2-upload-task-application-and-data-files) Cargar los archivos de aplicación y los archivos de entrada de la tarea en los contenedores.<br/>
[**Paso 3.**](#step-3-create-batch-pool) Cree un **grupo** de Batch.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** El grupo **StartTask** descarga los archivos binarios de la tarea (TaskApplication) en los nodos cuando se unen al grupo.<br/>
[**Paso 4.**](#step-4-create-batch-job) Cree un **trabajo** de Batch.<br/>
[**Paso 5.**](#step-5-add-tasks-to-job) Agregue **tareas** al trabajo.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** Las tareas se programan para ejecutarse en los nodos.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Cada tarea descarga sus datos de entrada de Azure Storage y comienza la ejecución.<br/>
[**Paso 6.**](#step-6-monitor-tasks) Supervisar las tareas.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** A medida que se completan las tareas, cargan sus datos de salida en Azure Storage.<br/>
[**Paso 7.**](#step-7-download-task-output) Descargar el resultado de la tarea de Storage

Como se ha indicado, no todas las soluciones de Batch realizan estos mismos pasos y se puede haber muchos otros; sin embargo, la aplicación de ejemplo *DotNetTutorial* muestra los procesos comunes que se encuentran en una solución de Batch.

## <a name="build-the-dotnettutorial-sample-project"></a>Creación del proyecto de ejemplo *DotNetTutorial*
Para poder ejecutar correctamente el ejemplo, debe especificar las credenciales de las cuentas tanto de Batch como de Storage en el archivo `Program.cs` del proyecto *DotNetTutorial*. Si aún no lo ha hecho, abra la solución en Visual Studio, para lo que debe hacer doble clic en el archivo de solución `DotNetTutorial.sln` . También puede abrirla desde el propio Visual Studio haciendo clic en **Archivo > Abrir > Proyecto o solución**.

Abra `Program.cs` en el proyecto *DotNetTutorial* . Luego, agregue las credenciales en la parte superior del archivo como se ha especificado:

```csharp
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

> [!IMPORTANT]
> Como ya se ha mencionado, ahora debe especificar las credenciales de una cuenta de almacenamiento de **uso general** en Azure Storage. Las aplicaciones de Batch usan almacenamiento de blobs en la cuenta de almacenamiento de **uso general**. No especifique las credenciales de una cuenta de Storage que se haya creado mediante la selección del tipo de cuenta *Blob Storage*.
>
>

Puede buscar las credenciales de las cuentas de Batch y de Storage en la hoja de la cuenta de cada uno de estos servicios en [Azure Portal][azure_portal]:

![Credenciales de Batch en el portal][9]
![Credenciales de Storage en el portal][10]<br/>

Tras actualizar el proyecto con sus credenciales, haga clic con el botón derecho en la solución en el Explorador de soluciones y haga clic en **Compilar solución**. Si se le solicita, confirme la restauración de los paquetes NuGet.

> [!TIP]
> Si los paquetes NuGet no se restauran automáticamente o si aparecen errores relacionados con la restauración de los paquetes, asegúrese de que el [Administrador de paquetes NuGet][nuget_packagemgr] está instalado y habilite la descarga de los paquetes que falten. Para habilitar la descarga de paquetes, consulte el tema sobre la [habilitación de la restauración de paquetes durante la compilación][nuget_restore].
>
>

En las secciones siguientes, se desglosarán los pasos que lleva a cabo la aplicación de ejemplo para procesar una carga de trabajo en el servicio Batch y se explicarán dichos pasos con detalle. Es aconsejable consultar la solución abierta en Visual Studio a medida que se avanza en este artículo, ya que no se tratan todas las líneas de código del ejemplo.

Para comenzar con el paso 1, vaya a la parte superior del método `MainAsync` en el archivo `Program.cs` del proyecto *DotNetTutorial*. Los pasos que se describen a continuación siguen aproximadamente la progresión de las llamadas al método en `MainAsync`.

## <a name="step-1-create-storage-containers"></a>Paso 1: Crear contenedores de Almacenamiento
![Crear contenedores en Azure Storage][1]
<br/>

Batch incluye compatibilidad integrada con la interacción con Azure Storage. Los contenedores de la cuenta de Storage proporcionarán los archivos que necesitarán las tareas que se ejecutan en la cuenta de Batch. Los contenedores también proporcionan un lugar para almacenar los datos de salida que producen las tareas. Lo primero que hace la aplicación cliente *DotNetTutorial* es crear tres contenedores en [Azure Blob Storage](../storage/common/storage-introduction.md):

* **application**: este contenedor almacenará la aplicación ejecutada por las tareas, así como todas sus dependencias, como los archivos DLL.
* **input**: las tareas descargarán los archivos de datos que se van a procesar desde el contenedor *input* .
* **output**: cuando las tareas completen el procesamiento de los archivos de entrada, cargarán los resultados en el contenedor *output* .

Para interactuar con una cuenta de Storage y crear contenedores, usamos la [biblioteca de cliente de Azure Storage para .NET][net_api_storage]. Creamos una referencia a la cuenta con [CloudStorageAccount][net_cloudstorageaccount] y, desde dicha referencia, creamos un objeto [CloudBlobClient][net_cloudblobclient]:

```csharp
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount =
    CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to
// blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

Usamos la referencia `blobClient` en toda la aplicación y la pasamos como parámetro a varios métodos. Podemos ver un ejemplo de esto en el bloque de código que aparece inmediatamente después de código anterior, donde llamamos a `CreateContainerIfNotExistAsync` para crear realmente los contenedores.

```csharp
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```csharp
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container =
            blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

Una vez creados los contenedores, la aplicación ya puede cargar los archivos que utilizarán las tareas.

> [!TIP]
> [Uso de Blob Storage desde .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) ofrece una buena introducción sobre cómo trabajar con blobs y contenedores de Azure Storage. Al empezar a trabajar con Batch, debe encontrarse cerca de la parte superior de la lista de lectura.
>
>

## <a name="step-2-upload-task-application-and-data-files"></a>Paso 2: Cargar la aplicación de tarea y los archivos de datos
![Cargar una aplicación de tarea y archivos de entrada (datos) en los contenedores][2]
<br/>

En la operación de carga de archivos, *DotNetTutorial* define en primer lugar las colecciones de las rutas de acceso a los archivos de **application** e **input**, tal como existen en la máquina local. Después cargará estos archivos en los contenedores que creó en el paso anterior.

```csharp
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

En `Program.cs` , hay dos métodos que intervienen en el proceso de carga:

* `UploadFilesToContainerAsync`: este método devuelve una colección de objetos [ResourceFile][net_resourcefile] (que se explican a continuación) y llama internamente a `UploadFileToContainerAsync` para cargar todos los archivos que se pasan en el parámetro *filePaths*.
* `UploadFileToContainerAsync`: es el método que realiza la carga de archivos y crea los objetos [ResourceFile][net_resourcefile]. Después de cargar el archivo, obtiene una firma de acceso compartido (SAS) para el archivo y devuelve un objeto ResourceFile que lo representa. A continuación también se tratan las firmas de acceso compartido.

```csharp
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### <a name="resourcefiles"></a>ResourceFiles
Un objeto [ResourceFile][net_resourcefile] proporciona tareas de Batch con la dirección URL a un archivo de Azure Storage que se descarga en un nodo de proceso antes de que la tarea se ejecute. La propiedad [ResourceFile.BlobSource][net_resourcefile_blobsource] especifica la dirección URL completa del archivo, tal como existe en Azure Storage. La dirección URL también puede incluir una firma de acceso compartido (SAS) que proporcione acceso seguro al archivo. La mayoría de los tipos de tareas de .NET de Batch contienen una propiedad *ResourceFiles*, que incluye:

* [CloudTask][net_task]
* [StartTask][net_pool_starttask]
* [JobPreparationTask][net_jobpreptask]
* [JobReleaseTask][net_jobreltask]

La aplicación de ejemplo DotNetTutorial no usa los tipos de tarea JobPreparationTask o JobReleaseTask. Para más información acerca de estos tipos de tarea, consulte [Ejecución de tareas de preparación y finalización de trabajos en nodos de proceso de Azure Batch](batch-job-prep-release.md).

### <a name="shared-access-signature-sas"></a>Firma de acceso compartido (SAS)
Las firmas de acceso compartido son cadenas que, cuando se incluyen como parte de una dirección URL, proporcionan acceso seguro a los contenedores y blobs de Azure Storage. La aplicación DotNetTutorial utiliza direcciones URL de firma de acceso compartido de blobs y contenedores, y muestra cómo obtener estas cadenas de firma de acceso compartido en el servicio Storage.

* **Firmas de acceso compartido de blobs**: la clase StartTask del grupo en DotNetTutorial usa firmas de acceso compartido de blobs al descargar los archivos binarios de la aplicación y los archivos de datos de entrada de Almacenamiento (consulte el paso 3). El método `UploadFileToContainerAsync` del archivo `Program.cs` de DotNetTutorial contiene el código que obtiene la firma de acceso compartido de cada blob. Para ello, llama a [CloudBlob.GetSharedAccessSignature][net_sas_blob].
* **Firmas de acceso compartido de contenedores**: cuando cada tarea finaliza su trabajo en el nodo de proceso, carga su archivo de salida en el contenedor *output* de Azure Storage. Para ello, TaskApplication usa una firma de acceso compartido de contenedor que proporciona acceso de escritura al contenedor como parte de la ruta de acceso al cargar el archivo. La obtención de la firma de acceso compartido del contenedor se realiza de manera similar que cuando se obtiene la firma de acceso compartido de un blob. En DotNetTutorial, observará que el método auxiliar `GetContainerSasUrl` llama a [CloudBlobContainer.GetSharedAccessSignature][net_sas_container] para hacerlo. En "Paso 6: Supervisar tareas", obtendrá más información acerca de la forma en que TaskApplication usa la firma de acceso compartido de un contenedor.

> [!TIP]
> Para más información acerca de cómo proporcionar acceso seguro a los datos de una cuenta de Storage, consulte la serie de dos partes acerca de las firmas de acceso compartido, [Parte 1: Descripción del modelo SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) y [Parte 2: Creación y uso de una SAS con Blob Storage](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md).
>
>

## <a name="step-3-create-batch-pool"></a>Paso 3: Crear el grupo de Batch
![Crear un grupo de Batch][3]
<br/>

Un **grupo** de Batch es una colección de nodos de proceso (máquinas virtuales) en los que Batch ejecuta tareas de un trabajo.

Después de cargar la aplicación y los archivos de datos a la cuenta de Storage con las API de Azure Storage, *DotNetTutorial* comienza a realizar llamadas al servicio de Batch con las API proporcionadas por la biblioteca .NET de Batch. El código crea primero una clase [BatchClient][net_batchclient]:

```csharp
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

Después, en el ejemplo se crea un grupo de nodos de proceso en la cuenta de Batch con una llamada a `CreatePoolIfNotExistsAsync`. `CreatePoolIfNotExistsAsync` usa el método [BatchClient.PoolOperations.CreatePool][net_pool_create] para crear un grupo en el servicio Batch:

```csharp
private static async Task CreatePoolIfNotExistAsync(BatchClient batchClient, string poolId, IList<ResourceFile> resourceFiles)
{
    CloudPool pool = null;
    try
    {
        Console.WriteLine("Creating pool [{0}]...", poolId);

        // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(), no pool is actually created in the
        // Batch service. This CloudPool instance is therefore considered "unbound," and we can modify its properties.
        pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicatedComputeNodes: 3,                                             // 3 compute nodes
            virtualMachineSize: "small",                                                // single-vCPU, 1.75 GB memory, 225 GB disk
            cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));   // Windows Server 2012 R2

        // Create and assign the StartTask that will be executed when compute nodes join the pool.
        // In this case, we copy the StartTask's resource files (that will be automatically downloaded
        // to the node by the StartTask) into the shared directory that all tasks will have access to.
        pool.StartTask = new StartTask
        {
            // Specify a command line for the StartTask that copies the task application files to the
            // node's shared directory. Every compute node in a Batch pool is configured with a number
            // of pre-defined environment variables that can be referenced by commands or applications
            // run by tasks.

            // Since a successful execution of robocopy can return a non-zero exit code (e.g. 1 when one or
            // more files were successfully copied) we need to manually exit with a 0 for Batch to recognize
            // StartTask execution success.
            CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
            ResourceFiles = resourceFiles,
            WaitForSuccess = true
        };

        await pool.CommitAsync();
    }
    catch (BatchException be)
    {
        // Swallow the specific error code PoolExists since that is expected if the pool already exists
        if (be.RequestInformation?.BatchError != null && be.RequestInformation.BatchError.Code == BatchErrorCodeStrings.PoolExists)
        {
            Console.WriteLine("The pool {0} already existed when we tried to create it", poolId);
        }
        else
        {
            throw; // Any other exception is unexpected
        }
    }
}
```

Cuando se crea un grupo con [CreatePool][net_pool_create], se especifican varios parámetros, como el número de nodos de proceso, el [tamaño de los nodos](../cloud-services/cloud-services-sizes-specs.md) y el sistema operativo de los nodos. En *DotNetTutorial*, usamos [CloudServiceConfiguration][net_cloudserviceconfiguration] para especificar Windows Server 2012 R2 en [Cloud Services](../cloud-services/cloud-services-guestos-update-matrix.md). 

También puede crear grupos de nodos de proceso que son instancias de Azure Virtual Machines (VM) especificando [VirtualMachineConfiguration][net_virtualmachineconfiguration] para el grupo. Puede crear un grupo de nodos de proceso de máquinas virtuales a partir de imágenes de Windows o [Linux](batch-linux-nodes.md). El origen de las imágenes de las máquinas virtuales puede ser:

- [Azure Virtual Machines Marketplace][vm_marketplace], que proporciona imágenes de Windows y Linux que están listas para su uso. 
- Una imagen personalizada preparada y proporcionada por el usuario. Para más información acerca de las imágenes personalizadas, consulte [Desarrollo de soluciones de procesos paralelos a gran escala con Batch](batch-api-basics.md#pool).

> [!IMPORTANT]
> Se cobrará por recursos de proceso en Batch. Para minimizar los costos, puede reducir `targetDedicatedComputeNodes` a 1 antes de ejecutar el ejemplo.
>
>

Además de estas propiedades del nodo físico, también puede especificar una clase [StartTask][net_pool_starttask] para el grupo. StartTask se ejecutará en cada nodo cuando este se una al grupo, así como cada vez que se reinicie. Esta clase es especialmente útil para instalar aplicaciones en nodos de ejecución antes de la ejecución de tareas. Por ejemplo, si las tareas procesan los datos mediante scripts de Python, puede usar una clase StartTask para instalar Python en los nodos de proceso.

En este ejemplo de aplicación, StartTask copia los archivos que descarga de Storage (que se especifican mediante la propiedad [StartTask][net_starttask].[ResourceFiles][net_starttask_resourcefiles]) desde el directorio de trabajo de StartTask hasta el directorio compartido al que pueden acceder *todas* las tareas que se ejecutan en el nodo. Básicamente, esto copia `TaskApplication.exe` y sus dependencias en el directorio compartido de cada nodo cuando el nodo se une al grupo, con el fin de que las tareas que se ejecuten en el nodo puedan acceder a él.

> [!TIP]
> La característica **paquetes de aplicación** de Azure Batch proporciona otra manera de implementar la aplicación en los nodos de proceso de un grupo. Consulte [Implementación de aplicaciones en nodos de proceso con paquetes de aplicaciones de Batch](batch-application-packages.md) para más información.
>
>

En el fragmento de código anterior, también cabe destacar el uso de dos variables de entorno en la propiedad *CommandLine* de StartTask: `%AZ_BATCH_TASK_WORKING_DIR%` y `%AZ_BATCH_NODE_SHARED_DIR%`. Cada nodo de proceso de un grupo de Batch se configura automáticamente con un número de variables de entorno específicas de Batch. Todos los procesos que ejecute una tarea tienen acceso a estas variables de entorno.

> [!TIP]
> Para más información acerca de las variables de entorno disponibles en los nodos de proceso de un grupo de Batch, así como acerca de los directorios de trabajo de las tareas, consulte las secciones [Configuración del entorno para las tareas](batch-api-basics.md#environment-settings-for-tasks) y [Archivos y directorios](batch-api-basics.md#files-and-directories) de [Introducción a las características de Azure Batch para desarrolladores](batch-api-basics.md).
>
>

## <a name="step-4-create-batch-job"></a>Paso 4: Crear el trabajo de Batch
![Crear un trabajo de Batch][4]<br/>

Un **trabajo** de Batch es una colección de tareas y está asociado a un grupo de nodos de proceso. Las tareas de un trabajo se ejecutan en los nodos de proceso del grupo asociado.

Se puede usar un trabajo no solo para la organización y seguimiento de las tareas en las cargas de trabajo relacionadas, sino también para imponer ciertas restricciones, como el tiempo máximo de ejecución del trabajo (y, por extensión, sus tareas), así como la prioridad del trabajo, en relación con los restantes trabajos de la cuenta de Batch. Sin embargo, en este ejemplo, el trabajo está asociado solo con el grupo que se creó en el paso 3. No hay propiedades adicionales configuradas.

Todos los trabajos de Batch están asociados a un grupo específico. Esta asociación indica en qué nodos se ejecutarán las tareas del trabajo. Para especificarlo, use la propiedad [CloudJob.PoolInformation][net_job_poolinfo], como se muestra en el siguiente fragmento de código.

```csharp
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

Ahora que se ha creado un trabajo, se agregan las tareas para realizar dicho trabajo.

## <a name="step-5-add-tasks-to-job"></a>Paso 5: Agregar tareas al trabajo
![Agregar tareas al trabajo][5]<br/>
*(1) Las tareas se agregan al trabajo, (2) las tareas se programan para ejecutarse en los nodos y (3) las tareas descargan los archivos de datos que se van a procesar*

Las **tareas** de Batch son unidades de trabajo individuales que se ejecutan en los nodos de proceso. Una tarea tiene una línea de comandos y ejecuta los scripts o archivos ejecutables que se especifican en la línea de comandos.

Para realizar el trabajo, las tareas deben agregarse a un trabajo. Cada tarea [CloudTask][net_task] se configura mediante una propiedad de línea de comandos y [ResourceFiles][net_task_resourcefiles] (igual que la tarea StartTask del grupo) que la tarea descarga en el nodo antes de que su línea de comandos se ejecute automáticamente. En el proyecto de ejemplo *DotNetTutorial* , cada tarea procesa un solo archivo. Por lo tanto, su colección ResourceFiles contiene un único elemento.

```csharp
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [!IMPORTANT]
> Cuando acceden a variables de entorno como `%AZ_BATCH_NODE_SHARED_DIR%` o ejecutan una aplicación que no se encuentra en el objeto `PATH` del nodo, las líneas de comandos de la tarea deben tener el prefijo `cmd /c`. Así se ejecutará explícitamente el intérprete de comandos y se dará la orden de finalización después de ejecutar el comando. Este requisito no es necesario si las tareas ejecutan una aplicación en el elemento `PATH` del nodo (como *robocopy.exe* o *powershell.exe*) y no se utilizan variables de entorno.
>
>

En el bucle `foreach` del fragmento de código anterior, puede ver que la línea de comandos de la tarea se construye de forma que se pasan tres argumentos de línea de comandos a *TaskApplication.exe*:

1. El **primer argumento** es la ruta de acceso del archivo que se va a procesar. Se trata de la ruta de acceso local al archivo, tal como existe en el nodo. Cuando se creó el objeto ResourceFile de `UploadFileToContainerAsync` , se usó el nombre de archivo para esta propiedad (como un parámetro del constructor ResourceFile). Esto indica que el archivo puede encontrarse en el mismo directorio que *TaskApplication.exe*.
2. El **segundo argumento** especifica que las *N* palabras más usadas deben escribirse en el archivo de salida. En el ejemplo, esto se codifica de forma rígida, con el fin de que las tres palabras más usadas se escriban en el archivo de salida.
3. El **tercer argumento** es la firma de acceso compartido (SAS) que proporciona acceso de escritura al contenedor **output** de Azure Storage. *TaskApplication.exe* utiliza la dirección URL de esta firma de acceso compartido al cargar el archivo de salida en Azure Storage. El código de este proceso se puede encontrar en el método `UploadFileToContainer` del archivo `Program.cs` del proyecto TaskApplication:

```csharp
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## <a name="step-6-monitor-tasks"></a>Paso 6: Supervisar tareas
![Supervisar tareas][6]<br/>
*La aplicación cliente (1) supervisa las tareas que comprueban la finalización y que el estado es correcto y (2) las tareas cargan los datos resultantes en Azure Storage.*

Cuando las tareas se agregan a un trabajo, automáticamente se ponen en cola y se programan para su ejecución en los nodos de ejecución del grupo asociado al trabajo. Según la configuración que especifique, Batch controla la administración de las colas, programación y reintentos de todas las tareas, así como otros cometidos de administración de tareas por usted.

Existen varios enfoques para supervisar la ejecución de tareas. DotNetTutorial muestra un ejemplo sencillo que informa únicamente sobre la finalización y los estados de error y de realización correcta de la tarea. En el método `MonitorTasks` del archivo `Program.cs` de DotNetTutorial, hay tres conceptos de .NET de Batch que merecen una explicación. Dichos conceptos se enumeran a continuación en su orden de aparición:

1. **ODATADetailLevel**: es esencial especificar [ODATADetailLevel][net_odatadetaillevel] en las operaciones de lista (por ejemplo, obtener una lista de las tareas de un trabajo) para garantizar el rendimiento de una aplicación de Batch. Agregue [Consulta eficaz del servicio Azure Batch](batch-efficient-list-queries.md) a su lista de lecturas si planea realizar cualquier tipo de supervisión del estado en las aplicaciones de Batch.
2. **TaskStateMonitor**: [TaskStateMonitor][net_taskstatemonitor] proporciona a aplicaciones de .NET de Batch utilidades auxiliares para la supervisión de los estados de las tareas. En `MonitorTasks`, *DotNetTutorial* espera hasta que todas las tareas alcancen el estado [TaskState.Completed][net_taskstate] dentro de un límite de tiempo. Luego finaliza el trabajo.
3. **TerminateJobAsync**: la finalización de un trabajo con [JobOperations.TerminateJobAsync][net_joboperations_terminatejob] (o JobOperations.TerminateJob de bloqueo) marca el trabajo como completado. Esta operación es esencial si la solución de Batch usa [JobReleaseTask][net_jobreltask]. Se trata de un tipo de tarea especial, que se describe en [areas de preparación y finalización de trabajos](batch-job-prep-release.md).

A continuación, se muestra el método `MonitorTasks` del archivo `Program.cs` de *DotNetTutorial*:

```csharp
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to  specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...",
        timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor
        = batchClient.Utilities.CreateTaskStateMonitor();

    try
    {
        await taskStateMonitor.WhenAll(tasks, TaskState.Completed, timeout);
    }
    catch (TimeoutException)
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);
        Console.WriteLine(failureMessage);
        return false;
    }

    await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

    // All tasks have reached the "Completed" state, however, this does not
    // guarantee all tasks completed successfully. Here we further check each task's
    // ExecutionInfo property to ensure that it did not encounter a failure
    // or return a non-zero exit code.

    // Update the detail level to populate only the task id and executionInfo
    // properties. We refresh the tasks below, and need only this information for
    // each task.
    detail.SelectClause = "id, executionInfo";

    foreach (CloudTask task in tasks)
    {
        // Populate the task's properties with the latest info from the Batch service
        await task.RefreshAsync(detail);

        if (task.ExecutionInformation.Result == TaskExecutionResult.Failure)
        {
            // A task with failure information set indicates there was a problem with the task. It is important to note that
            // the task's state can be "Completed," yet still have encountered a failure.

            allTasksSuccessful = false;

            Console.WriteLine("WARNING: Task [{0}] encountered a failure: {1}", task.Id, task.ExecutionInformation.FailureInformation.Message);
            if (task.ExecutionInformation.ExitCode != 0)
            {
                // A non-zero exit code may indicate that the application executed by the task encountered an error
                // during execution. As not every application returns non-zero on failure by default (e.g. robocopy),
                // your implementation of error checking may differ from this example.

                Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
            }
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## <a name="step-7-download-task-output"></a>Paso 7: Descargar el resultado de la tarea
![Descargar el resultado de la tarea de Storage][7]<br/>

Ahora que se ha completado el trabajo, el resultado de las tareas se puede descargar desde Azure Storage. Esto se realiza con una llamada a `DownloadBlobsFromContainerAsync` en el archivo `Program.cs` de *DotNetTutorial*:

```csharp
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [!NOTE]
> La llamada a `DownloadBlobsFromContainerAsync` en la aplicación *DotNetTutorial* especifica que los archivos se deben descargar en la carpeta `%TEMP%`. Puede modificar si lo desea esta ubicación de salida.
>
>

## <a name="step-8-delete-containers"></a>Paso 8: Eliminar contenedores
Como se le cobrará por los datos que residen en Azure Storage, siempre es una buena idea quitar los blobs que ya no sean necesarios para los trabajos de Batch. En el archivo `Program.cs` de DotNetTutorial, esto se realiza con tres llamadas al método auxiliar `DeleteContainerAsync`:

```csharp
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

El propio método simplemente obtiene una referencia al contenedor y después llama a [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete]:

```csharp
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## <a name="step-9-delete-the-job-and-the-pool"></a>Paso 9: Eliminar el trabajo y el grupo
En el último paso, se le pedirá que elimine el trabajo y el grupo que creó la aplicación DotNetTutorial. Aunque no se cobran los trabajos y las tareas, *sí* se cobran los nodos de proceso. Por consiguiente, se recomienda asignar solo los nodos necesarios. La eliminación de los grupos que no se usen puede formar parte del proceso de mantenimiento.

Las propiedades [JobOperations][net_joboperations] y [PoolOperations][net_pooloperations] de BatchClient tienen sus métodos de eliminación correspondientes, a los que se llama si el usuario confirma la eliminación:

```csharp
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [!IMPORTANT]
> Tenga en cuenta que los recursos de proceso se cobran; la eliminación de grupos sin usar reducirá el costo. Tenga en cuenta que al eliminar un grupo se eliminan todos los nodos de proceso del grupo y que los datos de los nodos no se podrán recuperar cuando se elimine el grupo.
>
>

## <a name="run-the-dotnettutorial-sample"></a>Ejecución del ejemplo de *DotNetTutorial*
Al ejecutar la aplicación de ejemplo, el resultado de la consola será similar al siguiente. Durante la ejecución, experimentará una pausa en `Awaiting task completion, timeout in 00:30:00...` mientras se inician los nodos de proceso del grupo. Use [Azure Portal][azure_portal] para supervisar el grupo, los nodos de proceso, el trabajo y las tareas durante la ejecución y después de ella. Use [Azure Portal][azure_portal] o el [Explorador de Azure Storage][storage_explorers] para ver los recursos de Storage (contenedores y blobs) que crea la aplicación.

El tiempo de ejecución es de **aproximadamente de 5 minutos** cuando se ejecuta la aplicación con su configuración predeterminada.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## <a name="next-steps"></a>Pasos siguientes
Puede realizar todos los cambios que desee en *DotNetTutorial* y *TaskApplication* para experimentar con distintos escenarios de proceso. Por ejemplo, pruebe a agregar a *TaskApplication* un retraso en la ejecución (por ejemplo, con [Thread.Sleep][net_thread_sleep]) para simular tareas de ejecución prolongada y supervisarlas en el portal. Pruebe a agregar más tareas o a ajustar el número de nodos de proceso. Agregue lógica para buscar un grupo existente y permitir su uso para reducir el tiempo de ejecución (*sugerencia*: extraiga `ArticleHelpers.cs` en el proyecto [Microsoft.Azure.Batch.Samples.Common][github_samples_common] de [azure-batch-samples][github_samples]).

Ahora que está familiarizado con el flujo de trabajo básico de una solución de Batch, ha llegado el momento de adentrarse en las características adicionales del servicio Batch.

* Consulte el artículo [Información general de las características de Azure Batch](batch-api-basics.md) , que es especialmente recomendable si no se conoce el servicio.
* Comience por los restantes artículos de desarrollo de Batch, en la sección **Desarrollo en profundidad** de la [ruta de aprendizaje de Batch][batch_learning_path].
* Consulte otra implementación del procesamiento de la carga de trabajo de "las N palabras más usadas" con Batch en el ejemplo [TopNWords][github_topnwords].
* Revise las [notas de la versión](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/Batch/DataPlane/changelog.md#azurebatch-release-notes) de la biblioteca de Batch para .NET para ver los cambios más recientes en la biblioteca.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[github_batchlabs]: https://azure.github.io/BatchLabs/
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://docs.nuget.org/consume/installing-nuget
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://storageexplorer.com/
[visual_studio]: https://www.visualstudio.com/vs/
[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Crear contenedores en Azure Storage"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Cargar una aplicación de tarea y archivos de entrada (datos) en los contenedores"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Crear el grupo de Batch"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Crear trabajo de Batch"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Agregar tareas al trabajo"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Supervisar tareas"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Descargar el resultado de la tarea de Storage"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Flujo de trabajo de soluciones de Batch (diagrama completo)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "Credenciales de Batch en el portal"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "Credenciales de Storage en el portal"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Flujo de trabajo de soluciones de Batch (diagrama mínimo)"
