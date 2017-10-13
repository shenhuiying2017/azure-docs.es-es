---
title: "Creación de una instancia de Azure Data Factory con .NET | Microsoft Docs"
description: "Cree una instancia de Azure Data Factory para copiar datos desde una ubicación de Azure Blob Storage a otra de la misma instancia de Blob Storage."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/06/2017
ms.author: jingwang
ms.openlocfilehash: d78176eca6bdbf32d6b4400ad2812dea98703d67
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-data-factory-and-pipeline-using-net-sdk"></a>Creación de una factoría de datos y una canalización con SDK de .NET
Azure Data Factory es un servicio de integración de datos basado en la nube que le permite crear flujos de trabajo basados en datos en la nube a fin de coordinar y automatizar el movimiento y la transformación de datos. Mediante Azure Data Factory, puede crear y programar flujos de trabajo orientados a datos (llamados canalizaciones) que pueden ingerir datos de almacenes de datos dispares, procesar o transformar los datos mediante servicios de proceso, como Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics y Azure Machine Learning, y publicar datos de salida en almacenes de datos, como Azure SQL Data Warehouse para que los consuman las aplicaciones de inteligencia empresarial (BI). 

En esta guía de inicio rápido se describe cómo usar SDK de .NET para crear una instancia de Azure Data Factory. La canalización de esta factoría de datos copia datos de una carpeta a otra en Azure Blob Storage.

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos
* **Cuenta de Almacenamiento de Azure**. El almacenamiento de blobs se puede usar como almacén de datos de **origen** y **receptor**. Si no dispone de una cuenta de Azure Storage, consulte el artículo [Creación de una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para aprender a crearla. 
* Cree un **contenedor de blobs** en Blob Storage, cree una **carpeta** de entrada en el contenedor y cargue algunos archivos en la carpeta. Puede usar herramientas como [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/) para conectarse a Azure Blob Storage, crear un contenedor de blobs, cargar el archivo de entrada y comprobar el archivo de salida.
* **Visual Studio** 2013, 2015 o 2017. En el tutorial de este artículo se usa Visual Studio 2017.
* **Descargue e instale [SDK de .NET de Azure](http://azure.microsoft.com/downloads/)**.
* **Cree una aplicación en Azure Active Directory**. Para hacerlo, siga [estas instrucciones](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Tome nota de los valores siguientes; los usará más adelante: **id. de aplicación**, **clave de autenticación** e **id. de inquilino**. Siga las instrucciones del mismo artículo para asignar la aplicación al rol "**Colaborador**". 
*  

## <a name="create-a-visual-studio-project"></a>Creación de un proyecto de Visual Studio

Con Visual Studio 2013, 2015 o 2017, cree una aplicación de consola .NET de C#.

1. Inicie **Visual Studio**.
2. Haga clic en **Archivo**, seleccione **Nuevo** y, luego, haga clic en **Proyecto**.
3. Seleccione **Visual C#** -> **Aplicación de consola (.NET Framework)** en la lista de tipos de proyecto situada a la derecha. Se requiere .NET versión 4.5.2 o superior.
4. Escriba **ADFv2QuickStart** para el Nombre.
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
    ```

2. Agregue el siguiente código al método **main** que define las variables. Reemplace los marcadores de posición con sus propios valores.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID where the data factory resides>";
    string resourceGroup = "<your resource group where the data factory resides>";
    // Currently, Data Factory V2 allows you to create data factories only in the East US and East US2 regions. 
    // Note that the data stores (Azure Storage, Azure SQL Database, etc.) and computes (HDInsight, etc.) used by data factory can be in other regions
    string region = "East US 2";
    string dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    // specify the container and input folder from which all files need to be copied to the output folder. 
    string inputBlobPath = "<the path to existing blob(s) to copy data from, e.g. containername/foldername>";
    //specify the contains and output folder where the files are copied
    string outputBlobPath = "<the blob path to copy data to, e.g. containername/foldername>";

    string storageLinkedServiceName = "AzureStorageLinkedService";  // name of the Azure Storage linked service
    string blobDatasetName = "BlobDataset";             // name of the blob dataset
    string pipelineName = "Adfv2QuickStartPipeline";    // name of the pipeline
    ```

3. Agregue el código siguiente al método **main** que crea una instancia de la clase **DataFactoryManagementClient**. Este objeto se usa para crear una factoría de datos, un servicio vinculado, conjuntos de datos y una canalización. También se usa para supervisar los detalles de ejecución de la canalización.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Crear una factoría de datos

Agregue el código siguiente al método **main** que crea una **factoría de datos**. 

```csharp
// Create a data factory
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-a-linked-service"></a>Creación de un servicio vinculado

Agregue el código siguiente al método **main** que crea un **servicio vinculado de Azure Storage**.

Los servicios vinculados se crean en una factoría de datos para vincular los almacenes de datos y los servicios de proceso con la factoría de datos. En esta guía de inicio rápido, basta con crear un servicio vinculado de Azure Storage para copiar el almacén de origen y el receptor. En el ejemplo, se denomina "AzureStorageLinkedService".

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

## <a name="create-a-dataset"></a>Crear un conjunto de datos

Agregue el código siguiente al método **main** que crea un **conjunto de datos de blob de Azure**.

Se define un conjunto de datos que representa los datos que se copian de un origen a un receptor. En este ejemplo, este conjunto de datos de blob hace referencia al servicio vinculado de Azure Storage que creó en el paso anterior. En el conjunto de datos se usa un parámetro cuyo valor se establece en una actividad que consume el conjunto de datos. El parámetro se usa para construir la ruta "folderPath" que apunta a la ubicación de los datos.

```csharp
// Create a Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }

        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Crear una canalización

Agregue el código siguiente al método **main** que crea una **canalización con una actividad de copia**.

En este ejemplo, esta canalización contiene una actividad y se usan dos parámetros: ruta de acceso de blob de entrada y de salida. Los valores para estos parámetros se establecen cuando se desencadena/ejecuta la canalización. La actividad de copia hace referencia al mismo conjunto de datos de blob creado en el paso anterior como entrada y salida. Cuando se usa el conjunto de datos como conjunto de datos de entrada, se especifica una ruta de acceso de entrada. Cuando se usa el conjunto de datos como conjunto de datos de salida, se especifica una ruta de acceso de salida. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Crear una ejecución de canalización

Agregue el código siguiente al método **main** que **desencadena una ejecución de canalización**.

Este código también establece los valores de los parámetros **inputPath** y **outputPath** especificados en la canalización con los valores reales de rutas de acceso de blob de origen y receptor.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Supervisión de una ejecución de canalización

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
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Ejecución del código

Compile e inicie la aplicación y, a continuación, compruebe la ejecución de la canalización.

La consola imprime el progreso de la creación de la factoría de datos, el servicio vinculado, los conjuntos de datos, la canalización y la ejecución de canalización. A continuación, comprueba el estado de la ejecución de canalización. Espere hasta que vea los detalles de ejecución de actividad de copia con el tamaño de los datos leídos/escritos. A continuación, use herramientas como [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/) para comprobar que los blobs se copian a "outputBlobPath" desde "inputBlobPath", como se especificó en las variables.

### <a name="sample-output"></a>Salida de ejemplo: 
```json
Creating data factory SPv2Factory0907...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@{dataset().path}",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "AzureStorageLinkedService",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "path": {
        "type": "String"
      }
    }
  }
}
Creating pipeline Adfv2QuickStartPipeline...
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
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.inputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.outputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "name": "CopyFromBlobToBlob"
      }
    ],
    "parameters": {
      "inputPath": {
        "type": "String"
      },
      "outputPath": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedCloudDataMovementUnits": 2,
    "billedDuration": 23
}

Press any key to exit...

```

## <a name="clean-up-resources"></a>Limpieza de recursos
Para eliminar la factoría de datos mediante programación, agregue las líneas de código siguientes al programa: 

```csharp
            Console.WriteLine("Deleting the data factory");
            client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>Pasos siguientes
La canalización de este ejemplo copia los datos de una ubicación a otra en una instancia de Azure Blob Storage. Consulte los [tutoriales](tutorial-copy-data-dot-net.md) para obtener información acerca del uso de Data Factory en otros escenarios. 
