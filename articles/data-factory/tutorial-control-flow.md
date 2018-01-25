---
title: "Bifurcación de la canalización de Azure Data Factory | Microsoft Docs"
description: "Obtenga información sobre cómo controlar el flujo de datos en Azure Data Factory mediante la bifurcación y el encadenamiento de actividades."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: 8259c1bd52cfd0641148dc09404debaf59640b45
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="branching-and-chaining-activities-in-a-data-factory-pipeline"></a>Actividades de bifurcación y encadenamiento en una canalización de Data Factory
En este tutorial, creará una canalización de Data Factory que muestra algunas de las características del flujo de control. Esta canalización realiza una copia simple de un contenedor en Azure Blob Storage a otro contenedor de la misma cuenta de almacenamiento. Si la actividad de copia se realiza correctamente, será necesario que envíe los detalles de la operación de copia correcta (por ejemplo, la cantidad de datos escritos) en un correo electrónico de operación correcta. Si se produce un error en la actividad de copia, deberá enviar los detalles del error de la copia (por ejemplo, el mensaje de error) en un correo electrónico de operación incorrecta. A lo largo del tutorial, verá cómo pasar parámetros.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte la [documentación de la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Descripción general del escenario: ![información general](media/tutorial-control-flow/overview.png)

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Creación de un servicio vinculado de Azure Storage
> * Creación de un conjunto de datos del blob de Azure
> * Creación de una canalización que contiene una actividad de copia y una actividad web
> * Envío de los resultados de las actividades en actividades subsiguientes
> * Uso de las variables del sistema y del paso de parámetros
> * Inicio de la ejecución de una canalización
> * Supervisión de las ejecuciones de canalización y actividad

En este tutorial se usa SDK de .NET. Puede usar otros mecanismos para interactuar con Azure Data Factory. Consulte "Inicios rápidos" en la tabla de contenido.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>requisitos previos

* **Cuenta de Azure Storage**. Blob Storage se puede usar como almacén de datos de **origen**. Si no tiene una cuenta de almacenamiento de Azure, consulte el artículo [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para ver los pasos para su creación.
* **Azure SQL Database**. La base de datos se puede usar como almacén de datos **receptor**. Si no tiene ninguna instancia de Azure SQL Database, consulte el artículo [Creación de una instancia de Azure SQL Database](../sql-database/sql-database-get-started-portal.md) para ver los pasos para su creación.
* **Visual Studio** 2013, 2015 o 2017. En el tutorial de este artículo se usa Visual Studio 2017.
* **Descargue e instale [SDK de .NET de Azure](http://azure.microsoft.com/downloads/)**.
* **Cree una aplicación en Azure Active Directory**. Para hacerlo, siga [estas instrucciones](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Tome nota de los valores siguientes; los usará en pasos posteriores: **id. de aplicación**, **clave de autenticación** e **id. de inquilino**. Siga las instrucciones del mismo artículo para asignar la aplicación al rol "**Colaborador**".

### <a name="create-blob-table"></a>Creación de la tabla de blobs

1. Inicie el Bloc de notas. Copie el texto siguiente y guárdelo como archivo **input.txt** en el disco.

    ```
    John|Doe
    Jane|Doe
    ```
2. Use herramientas como [Explorador de Azure Storage](http://storageexplorer.com/) para crear el contenedor **adfv2branch** y cargar el archivo **input.txt** en el contenedor.

## <a name="create-visual-studio-project"></a>Creación de un proyecto de Visual Studio

Con Visual Studio 2015 o 2017, cree una aplicación de consola .NET de C#.

1. Inicie **Visual Studio**.
2. Haga clic en **Archivo**, seleccione **Nuevo** y, luego, haga clic en **Proyecto**. Se requiere .NET versión 4.5.2 o posterior.
3. Seleccione **Visual C#** -> **Aplicación de consola (.NET Framework)** en la lista de tipos de proyecto situada a la derecha.
4. En el nombre, escriba **ADFv2BranchTutorial**.
5. Haga clic en **Aceptar** para crear el proyecto.

## <a name="install-nuget-packages"></a>Instalación de paquetes NuGet

1. Haga clic en **Herramientas** -> **Administrador de paquetes NuGet** -> **Consola del administrador de paquetes**.
2. En **Consola del Administrador de paquetes**, ejecute los comandos siguientes para instalar paquetes:

    ```
    Install-Package Microsoft.Azure.Management.DataFactory -Prerelease
    Install-Package Microsoft.Azure.Management.ResourceManager -Prerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Creación de un cliente de factoría de datos

1. Abra **Program.cs** e incluya las siguientes instrucciones para agregar referencias a espacios de nombres.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;

2. Add these static variables to the **Program class**. Replace place-holders with your own values. Currently, Data Factory V2 allows you to create data factories only in the East US, East US2, and West Europe regions. The data stores (Azure Storage, Azure SQL Database, etc.) and computes (HDInsight, etc.) used by data factory can be in other regions.

    ```csharp
        // Set variables
        static string tenantID = "<tenant ID>";
        static string applicationId = "<application ID>";
        static string authenticationKey = "<Authentication key for your application>";
        static string subscriptionId = "<Azure subscription ID>";
        static string resourceGroup = "<Azure resource group name>";

        static string region = "East US";
        static string dataFactoryName = "<Data factory name>";

        // Specify the source Azure Blob information
        static string storageAccount = "<Azure Storage account name>";
        static string storageKey = "<Azure Storage account key>";
        // confirm that you have the input.txt file placed in th input folder of the adfv2branch container. 
        static string inputBlobPath = "adfv2branch/input";
        static string inputBlobName = "input.txt";
        static string outputBlobPath = "adfv2branch/output";
        static string emailReceiver = "<specify email address of the receiver>";

        static string storageLinkedServiceName = "AzureStorageLinkedService";
        static string blobSourceDatasetName = "SourceStorageDataset";
        static string blobSinkDatasetName = "SinkStorageDataset";
        static string pipelineName = "Adfv2TutorialBranchCopy";

        static string copyBlobActivity = "CopyBlobtoBlob";
        static string sendFailEmailActivity = "SendFailEmailActivity";
        static string sendSuccessEmailActivity = "SendSuccessEmailActivity";
    
    ```

3. Agregue el código siguiente al método **Main** que crea una instancia de la clase **DataFactoryManagementClient**. Este objeto se usa para crear la factoría de datos, el servicio vinculado, los conjuntos de datos y la canalización. También se usa para supervisar los detalles de ejecución de la canalización.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Crear una factoría de datos
Cree la función "CreateOrUpdateDataFactory" en el archivo Program.cs:

```csharp
static Factory CreateOrUpdateDataFactory(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating data factory " + dataFactoryName + "...");
    Factory resource = new Factory
    {
        Location = region
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));

    Factory response;
    {
        response = client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, resource);
    }

    while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
    {
        System.Threading.Thread.Sleep(1000);
    }
    return response;
}
```



Agregue el código siguiente al método **Main** que crea una **factoría de datos**. 

```csharp
Factory df = CreateOrUpdateDataFactory(client);
```

## <a name="create-an-azure-storage-linked-service"></a>Creación de un servicio vinculado de Azure Storage
Cree la función de "StorageLinkedServiceDefinition" en el archivo Program.cs:

```csharp
static LinkedServiceResource StorageLinkedServiceDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");
    AzureStorageLinkedService storageLinkedService = new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
    LinkedServiceResource linkedService = new LinkedServiceResource(storageLinkedService, name:storageLinkedServiceName);
    return linkedService;
}
```
Agregue el código siguiente al método **main** que crea un **servicio vinculado de Azure Storage**. Obtenga más información en la sección [Azure Blob linked service properties](connector-azure-blob-storage.md#linked-service-properties) (Propiedades del servicio vinculado del blob de Azure) sobre los detalles y las propiedades que se admiten.

```csharp
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
```

## <a name="create-datasets"></a>Creación de conjuntos de datos

En esta sección, se crean dos conjuntos de datos: uno para el origen y otro para el receptor. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Creación de un conjunto de datos para el blob de Azure de origen
Agregue el código siguiente al método **Main** que crea un **conjunto de datos de blob de Azure**. Obtenga más información en la sección [Azure Blob dataset properties](connector-azure-blob-storage.md#dataset-properties) (Propiedades del conjunto de datos del blob de Azure) sobre los detalles y las propiedades que se admiten.

Se define un conjunto de datos que representa los datos de origen del blob de Azure. Este conjunto de datos de blob hace referencia al servicio vinculado de Azure Storage que creó en el paso anterior y describe:

- La ubicación del blob que se debe copiar de: **FolderPath** y **FileName**;
- Tenga en cuenta el uso de los parámetros para FolderPath. "sourceBlobContainer" es el nombre del parámetro y la expresión se reemplaza con los valores pasados en la canalización de ejecución. La sintaxis para definir los parámetros es `@pipeline().parameters.<parameterName>`.

Cree la función de "SourceBlobDatasetDefinition" en el archivo Program.cs.

```csharp
static DatasetResource SourceBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSourceDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    { 
        FolderPath = new Expression { Value = "@pipeline().parameters.sourceBlobContainer" },
        FileName = inputBlobName,
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name:blobSourceDatasetName);
    return dataset;
}
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Creación de un conjunto de datos para el blob de Azure receptor

Cree la función de "SourceBlobDatasetDefinition" en el archivo Program.cs.

```csharp
static DatasetResource SinkBlobDatasetDefinition(DataFactoryManagementClient client)
{
    Console.WriteLine("Creating dataset " + blobSinkDatasetName + "...");
    AzureBlobDataset blobDataset = new AzureBlobDataset
    {
        FolderPath = new Expression { Value = "@pipeline().parameters.sinkBlobContainer" },
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        }
    };
    Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
    DatasetResource dataset = new DatasetResource(blobDataset, name: blobSinkDatasetName);
    return dataset;
}
```

Agregue el código siguiente al método **Main** que crea los conjuntos de datos del blob de Azure de origen y recepción. 

```csharp
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));

client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));
```

## <a name="create-a-c-class-emailrequest"></a>Creación de una clase C#: EmailRequest
En el proyecto de C#, cree una clase denominada **EmailRequest**. Esto define qué propiedades envía la canalización en la solicitud del cuerpo al enviar un correo electrónico. En este tutorial, la canalización envía cuatro propiedades desde la canalización al correo electrónico:

- **Message**: cuerpo del correo electrónico. En el caso de que una copia se realice correctamente, esta propiedad contiene los detalles de la ejecución (número de datos escritos). En el caso de que una copia se realice de forma incorrecta, esta propiedad contiene los detalles del error.
- **Data factory name**: nombre de la factoría de datos
- **Pipeline name**: nombre de la canalización
- **Receiver**: parámetro que se pasa Esta propiedad especifica el destinatario del correo electrónico.

```csharp
    class EmailRequest
    {
        [Newtonsoft.Json.JsonProperty(PropertyName = "message")]
        public string message;

        [Newtonsoft.Json.JsonProperty(PropertyName = "dataFactoryName")]
        public string dataFactoryName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "pipelineName")]
        public string pipelineName;

        [Newtonsoft.Json.JsonProperty(PropertyName = "receiver")]
        public string receiver;

        public EmailRequest(string input, string df, string pipeline, string receiverName)
        {
            message = input;
            dataFactoryName = df;
            pipelineName = pipeline;
            receiver = receiverName;
        }
    }
```
## <a name="create-email-workflow-endpoints"></a>Creación de puntos de conexión de flujo de trabajo del correo electrónico
Para desencadenar el envío de un correo electrónico, use [Logic Apps](../logic-apps/logic-apps-overview.md) a fin de definir el flujo de trabajo. Para obtener más información acerca de cómo crear un flujo de trabajo de una aplicación lógica, consulte [Cómo crear una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

### <a name="success-email-workflow"></a>Flujo de trabajo del correo electrónico de operación correcta 
Cree un flujo de trabajo de aplicación lógica denominado `CopySuccessEmail`. Defina el desencadenador del flujo de trabajo como `When an HTTP request is received` y agregue una acción de `Office 365 Outlook – Send an email`.

![Flujo de trabajo del correo electrónico de operación correcta](media/tutorial-control-flow/success-email-workflow.png)

Para el desencadenador de la solicitud, rellene `Request Body JSON Schema` con el JSON siguiente:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```
Esto se alinea con la clase **EmailRequest** creada en la sección anterior. 

Su solicitud debería tener un aspecto siguiente a la del Diseñador de aplicación lógica:

![Diseñador de aplicación lógica: solicitud](media/tutorial-control-flow/logic-app-designer-request.png)

Para la acción **Enviar correo electrónico**, personalice el formato del correo electrónico. Para ello, use las propiedades que se pasan en el esquema JSON del cuerpo de solicitud. Este es un ejemplo:

![Diseñador de aplicación lógica: acción de envío de correo electrónico](media/tutorial-control-flow/send-email-action.png)

Tome nota de la URL de solicitud POST HTTP para el flujo de trabajo del correo electrónico de operación correcta:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="fail-email-workflow"></a>Flujo de trabajo del correo electrónico de operación incorrecta 
Clone su **CopySuccessEmail** y crear otro flujo de trabajo de Logic Apps de **CopyFailEmail**. En el desencadenador de solicitudes, `Request Body JSON schema` es el mismo. Simplemente, cambie el formato del correo electrónico, por ejemplo, la parte `Subject`, para adaptarlo para que sea un correo electrónico de operación incorrecta. Este es un ejemplo:

![Diseñador de aplicación lógica: flujo de trabajo del correo electrónico de operación incorrecta](media/tutorial-control-flow/fail-email-workflow.png)

Tome nota de la URL de solicitud POST HTTP para el flujo de trabajo del correo electrónico de operación incorrecta:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Ahora debería tener dos URL de flujo de trabajo:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```
## <a name="create-a-pipeline"></a>Crear una canalización
Agregue el código siguiente al método Main que crea una canalización con una actividad de copia y la propiedad dependsOn. En este tutorial, la canalización contiene una actividad: la actividad de copia, que toma el conjunto de datos del blob como origen y otro conjunto de datos del blob como receptor. Durante la actividad de copia que se realiza de forma correcta e incorrecta, llama a distintas tareas de correo electrónico.

En esta canalización, use las siguientes características:

- Parámetros
- Actividad web
- Dependencia de actividades
- Uso de la salida de una actividad como la entrada para la actividad subsiguiente

Vamos a desglosar la canalización siguiente en varias partes:

```csharp

static PipelineResource PipelineDefinition(DataFactoryManagementClient client)
        {
            Console.WriteLine("Creating pipeline " + pipelineName + "...");
            PipelineResource resource = new PipelineResource
            {
                Parameters = new Dictionary<string, ParameterSpecification>
                {
                    { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                    { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
                    { "receiver", new ParameterSpecification { Type = ParameterType.String } }

                },
                Activities = new List<Activity>
                {
                    new CopyActivity
                    {
                        Name = copyBlobActivity,
                        Inputs = new List<DatasetReference>
                        {
                            new DatasetReference
                            {
                                ReferenceName = blobSourceDatasetName
                            }
                        },
                        Outputs = new List<DatasetReference>
                        {
                            new DatasetReference
                            {
                                ReferenceName = blobSinkDatasetName
                            }
                        },
                        Source = new BlobSource { },
                        Sink = new BlobSink { }
                    },
                    new WebActivity
                    {
                        Name = sendSuccessEmailActivity,
                        Method = WebActivityMethod.POST,
                        Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/00000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000000",
                        Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                        DependsOn = new List<ActivityDependency>
                        {
                            new ActivityDependency
                            {
                                Activity = copyBlobActivity,
                                DependencyConditions = new List<String> { "Succeeded" }
                            }
                        }
                    },
                    new WebActivity
                    {
                        Name = sendFailEmailActivity,
                        Method =WebActivityMethod.POST,
                        Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/000000000000000000000000000000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=0000000000000000000000000000000000000000000",
                        Body = new EmailRequest("@{activity('CopyBlobtoBlob').error.message}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
                        DependsOn = new List<ActivityDependency>
                        {
                            new ActivityDependency
                            {
                                Activity = copyBlobActivity,
                                DependencyConditions = new List<String> { "Failed" }
                            }
                        }
                    }
                }
            };
            Console.WriteLine(SafeJsonConvert.SerializeObject(resource, client.SerializationSettings));
            return resource;
        }
```
Agregue el código siguiente al método **Main** que crea la canalización:

```
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));
```
### <a name="parameters"></a>Parámetros
La primera sección de nuestra canalización define parámetros. 

- sourceBlobContainer: parámetro de la canalización usado por el conjunto de datos del blob de origen.
- sinkBlobContainer: parámetro de la canalización usado por el conjunto de datos del blob receptor.
- receiver: parámetro usado por las dos actividades Web de la canalización que envían correos electrónicos correos o con errores al receptor cuya dirección de correo electrónico especifica este parámetro.


```csharp
Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "sourceBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "sinkBlobContainer", new ParameterSpecification { Type = ParameterType.String } },
        { "receiver", new ParameterSpecification { Type = ParameterType.String } }
    },
```
### <a name="web-activity"></a>Actividad web
La actividad web permite una llamada a cualquier punto de conexión de REST. Para más información sobre la actividad, consulte el artículo [Web Activity](control-flow-web-activity.md) (Actividad web). Esta canalización usa una actividad web para llamar al flujo de trabajo de correo electrónico de Logic Apps. Debe crear dos actividades web: una que llame al flujo de trabajo **CopySuccessEmail** y otra que llame a **CopyFailWorkFlow**.

```csharp
        new WebActivity
        {
            Name = sendCopyEmailActivity,
            Method = WebActivityMethod.POST,
            Url = "https://prodxxx.eastus.logic.azure.com:443/workflows/12345",
            Body = new EmailRequest("@{activity('CopyBlobtoBlob').output.dataWritten}", "@{pipeline().DataFactory}", "@{pipeline().Pipeline}", "@pipeline().parameters.receiver"),
            DependsOn = new List<ActivityDependency>
            {
                new ActivityDependency
                {
                    Activity = copyBlobActivity,
                    DependencyConditions = new List<String> { "Succeeded" }
                }
            }
        }
```
En la propiedad "Url", pegue los puntos de conexión de la URL de solicitud del flujo de trabajo de Logic Apps según corresponda. En la propiedad "body", pase una instancia de la clase "EmailRequest". La solicitud de correo electrónico contiene las siguientes propiedades:

- Message, que pasa el valor `@{activity('CopyBlobtoBlob').output.dataWritten`. Tiene acceso a una propiedad de la actividad de copia anterior y pasa el valor de dataWritten. En caso de error, pasa la salida de error en lugar de `@{activity('CopyBlobtoBlob').error.message`.
- Data Factory Name, que pasa el valor `@{pipeline().DataFactory}`. Se trata de una variable del sistema, lo que le permite obtener acceso al nombre de la factoría de datos correspondiente. Para obtener una lista de las variables del sistema, vea el artículo [System Variables](control-flow-system-variables.md) (Variables del sistema).
- Pipeline Name, que pasa el valor `@{pipeline().Pipeline}`. También es una variable del sistema, lo que le permite obtener acceso al nombre de canalización correspondiente. 
- Receiver, que pasa el valor "@pipeline(). parameters.receiver") y accede a los parámetros de la canalización.
 
Este código crea una nueva dependencia de actividad, en función de la actividad de copia anterior que ejecuta correctamente.

## <a name="create-a-pipeline-run"></a>Creación de una ejecución de canalización
Agregue el código siguiente al método **Main** que **desencadena una ejecución de canalización**.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="main-class"></a>Clase Main 
El método Main final debe tener el aspecto siguiente. Compile y ejecute su programa para desencadenar una ejecución de canalización.

```csharp
// Authenticate and create a data factory management client
var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };

Factory df = CreateOrUpdateDataFactory(client);

client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, StorageLinkedServiceDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSourceDatasetName, SourceBlobDatasetDefinition(client));
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobSinkDatasetName, SinkBlobDatasetDefinition(client));

client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, PipelineDefinition(client));

Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> arguments = new Dictionary<string, object>
{
    { "sourceBlobContainer", inputBlobPath },
    { "sinkBlobContainer", outputBlobPath },
    { "receiver", emailReceiver }
};

CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, arguments).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Supervisar una ejecución de canalización
1. Agregue el código siguiente al método **Main** para comprobar continuamente el estado de la ejecución de canalización hasta que termine de copiar los datos.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Agregue el código siguiente al método **Main** que recupera detalles de la ejecución de actividad de copia, como el tamaño de los datos leídos o escritos.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");

    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
 
    if (pipelineRun.Status == "Succeeded")
    {
        Console.WriteLine(activityRuns.First().Output);
        //SaveToJson(SafeJsonConvert.SerializeObject(activityRuns.First().Output, client.SerializationSettings), "ActivityRunResult.json", folderForJsons);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Ejecución del código
Compile e inicie la aplicación y, a continuación, compruebe la ejecución de la canalización.
La consola imprime el progreso de la creación de la factoría de datos, el servicio vinculado, los conjuntos de datos, la canalización y la ejecución de canalización. A continuación, comprueba el estado de la ejecución de canalización. Espere hasta que vea los detalles de ejecución de actividad de copia con el tamaño de los datos leídos/escritos. A continuación, use herramientas como Explorador de Azure Storage para comprobar que los blobs se copian a "outputBlobPath" desde "inputBlobPath", como se especificó en las variables.

**Salida de ejemplo:**

```json
Creating data factory DFTutorialTest...
{
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "type": "AzureStorage",
  "typeProperties": {
    "connectionString": {
      "type": "SecureString",
      "value": "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***"
    }
  }
}
Creating dataset SourceStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sourceBlobContainer"
    },
    "fileName": "input.txt"
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating dataset SinkStorageDataset...
{
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "type": "Expression",
      "value": "@pipeline().parameters.sinkBlobContainer"
    }
  },
  "linkedServiceName": {
    "type": "LinkedServiceReference",
    "referenceName": "AzureStorageLinkedService"
  }
}
Creating pipeline Adfv2TutorialBranchCopy...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SourceStorageDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SinkStorageDataset"
          }
        ],
        "name": "CopyBlobtoBlob"
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendSuccessEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Succeeded"
            ]
          }
        ]
      },
      {
        "type": "WebActivity",
        "typeProperties": {
          "method": "POST",
          "url": "https://xxx.eastus.logic.azure.com:443/workflows/... ",
          "body": {
            "message": "@{activity('CopyBlobtoBlob').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
          }
        },
        "name": "SendFailEmailActivity",
        "dependsOn": [
          {
            "activity": "CopyBlobtoBlob",
            "dependencyConditions": [
              "Failed"
            ]
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      },
      "receiver": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 00000000-0000-0000-0000-0000000000000
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 20,
  "dataWritten": 20,
  "copyDuration": 4,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
}
{}

Press any key to exit...
```

## <a name="next-steps"></a>pasos siguientes
En este tutorial, realizó los pasos siguientes: 

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Creación de un servicio vinculado de Azure Storage
> * Creación de un conjunto de datos del blob de Azure
> * Creación de una canalización que contiene una actividad de copia y una actividad web
> * Envío de los resultados de las actividades en actividades subsiguientes
> * Uso de las variables del sistema y del paso de parámetros
> * Inicio de la ejecución de una canalización
> * Supervisión de las ejecuciones de canalización y actividad

Ahora puede continuar a la sección de conceptos para obtener más información sobre Azure Data Factory.
> [!div class="nextstepaction"]
>[Canalizaciones y actividades](concepts-pipelines-activities.md)
