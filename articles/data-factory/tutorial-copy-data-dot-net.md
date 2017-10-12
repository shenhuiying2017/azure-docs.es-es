---
title: Copia de datos de Azure Blob Storage a SQL Database | Microsoft Docs
description: "Este tutorial proporciona instrucciones detalladas sobre cómo copiar datos desde Azure Blob Storage a Azure SQL Database."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: 6f1a93c2906eaab82dcfb9bae1ee4a54dce300bd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-azure-blob-to-azure-sql-database-using-azure-data-factory"></a>Copia de datos del blob de Azure a Azure SQL Database mediante Azure Data Factory
Azure Data Factory es un servicio de integración de datos basado en la nube que le permite crear flujos de trabajo basados en datos en la nube a fin de coordinar y automatizar el movimiento y la transformación de datos. Mediante Azure Data Factory, puede crear y programar flujos de trabajo orientados a datos (llamados canalizaciones) que pueden ingerir datos de almacenes de datos dispares, procesar o transformar los datos mediante servicios de proceso, como Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics y Azure Machine Learning, y publicar datos de salida en almacenes de datos, como Azure SQL Data Warehouse para que los consuman las aplicaciones de inteligencia empresarial (BI). 

En este tutorial, creará una canalización de Data Factory que copia datos de Azure Blob Storage a Azure SQL Database. El patrón de configuración de este tutorial se aplica a la copia de un almacén de datos basado en archivos a un almacén de datos relacional. Para obtener una lista de los almacenes de datos que se admiten como orígenes y receptores, consulte la tabla [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats) (Almacenes de datos admitidos).

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Creación de servicios vinculados con Azure SQL Database y Azure Storage
> * Creación de conjuntos de datos de Azure SQL Database y el blob de Azure
> * Creación de una canalización que contiene una actividad de copia
> * Inicio de la ejecución de una canalización.
> * Supervisión de las ejecuciones de canalización y actividad.

En este tutorial se usa SDK de .NET. Puede usar otros mecanismos para interactuar con Azure Data Factory. Consulte los ejemplos de la sección "Inicios rápidos".

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

* **Cuenta de Almacenamiento de Azure**. Blob Storage se puede usar como almacén de datos de **origen**. Si no tiene una cuenta de almacenamiento de Azure, consulte el artículo [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para ver los pasos para su creación.
* **Base de datos de SQL Azure**. La base de datos se puede usar como almacén de datos **receptor**. Si no tiene ninguna instancia de Azure SQL Database, consulte el artículo [Creación de una instancia de Azure SQL Database](../sql-database/sql-database-get-started-portal.md) para ver los pasos para su creación.
* **Visual Studio** 2015 o 2017. En el tutorial de este artículo se usa Visual Studio 2017.
* **Descargue e instale [SDK de .NET de Azure](http://azure.microsoft.com/downloads/)**.
* **Cree una aplicación en Azure Active Directory**. Para ello, siga [estas instrucciones](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Tome nota de los valores siguientes; los usará en pasos posteriores: **id. de aplicación**, **clave de autenticación** e **id. de inquilino**. Siga las instrucciones del mismo artículo para asignar la aplicación al rol "**Colaborador**".

### <a name="create-a-blob-and-a-sql-table"></a>Creación de un blob y una tabla SQL

Ahora, prepare su blob de Azure y su instancia de Azure SQL Database para el tutorial. Para ello, siga los pasos siguientes:

#### <a name="create-a-source-blob"></a>Creación de un blob de origen

1. Inicie el Bloc de notas. Copie el texto siguiente y guárdelo como archivo **inputEmp.txt** en el disco.

    ```
    John|Doe
    Jane|Doe
    ```

2. Use herramientas como [Explorador de Azure Storage](http://storageexplorer.com/) para crear el contenedor **adfv2tutorial** y cargar el archivo **inputEmp.txt** en el contenedor.

#### <a name="create-a-sink-sql-table"></a>Creación de una tabla SQL receptora

1. Use el siguiente script de SQL para crear la tabla **dbo.emp** en su instancia de Azure SQL Database.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Permita que los servicios de Azure accedan a SQL Server. Asegúrese de que la configuración **Permitir el acceso a los servicios de Azure** está **Activada** para SQL Server de Azure para que el servicio Data Factory pueda acceder a SQL Server de Azure. Para comprobar y activar esta configuración, realice los siguientes pasos:

    1. Haga clic en el concentrador **Más servicios** a la izquierda y haga clic en **Servidores SQL**.
    2. Seleccione el servidor y haga clic en **Firewall** en **CONFIGURACIÓN**.
    3. En la hoja **Configuración de firewall**, haga clic en **Activar** para **Permitir el acceso a los servicios de Azure**.


## <a name="create-a-visual-studio-project"></a>Creación de un proyecto de Visual Studio

Con Visual Studio 2015 o 2017, cree una aplicación de consola .NET de C#.

1. Inicie **Visual Studio**.
2. Haga clic en **Archivo**, seleccione **Nuevo** y, luego, haga clic en **Proyecto**.
3. Seleccione **Visual C#** -> **Aplicación de consola (.NET Framework)** en la lista de tipos de proyecto situada a la derecha. Se requiere .NET versión 4.5.2 o posterior.
4. En el nombre, escriba **ADFv2Tutorial**.
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

2. Add the following code to the **Main** method that sets variables. Replace place-holders with your own values.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID to create the factory>";
    string resourceGroup = "<your resource group to create the factory>";

    // Note that the data stores (Azure Storage, Azure SQL Database, etc.) and computes (HDInsight, etc.) used by a data factory can be in other regions.
    string region = "East US";
    string dataFactoryName = "<specify the name of a data factory to create. It must be globally unique.>";

    // Specify the source Azure Blob information
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    string inputBlobPath = "adfv2tutorial/";
    string inputBlobName = "inputEmp.txt";

    // Specify the sink Azure SQL Database information
    string azureSqlConnString = "Server=tcp:<your server name>.database.windows.net,1433;Database=<your database name>;User ID=<your username>@<your server name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30";
    string azureSqlTableName = "dbo.emp";

    string storageLinkedServiceName = "AzureStorageLinkedService";
    string sqlDbLinkedServiceName = "AzureSqlDbLinkedService";
    string blobDatasetName = "BlobDataset";
    string sqlDatasetName = "SqlDataset";
    string pipelineName = "Adfv2TutorialBlobToSqlCopy";
    ```

3. Agregue el código siguiente al método **Main** que crea una instancia de la clase **DataFactoryManagementClient**. Este objeto se usa para crear una factoría de datos, un servicio vinculado, los conjuntos de datos y una canalización. También se usa para supervisar los detalles de ejecución de la canalización.

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
Console.WriteLine("Creating a data factory " + dataFactoryName + "...");
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

## <a name="create-linked-services"></a>Crear servicios vinculados

En este tutorial se crean dos servicios vinculados para el origen y el receptor respectivamente:

### <a name="create-an-azure-storage-linked-service"></a>Creación de un servicio vinculado de Azure Storage

Agregue el código siguiente al método **Main** que crea un **servicio vinculado a Azure Storage**. Obtenga más información en la sección [Azure Blob linked service properties](connector-azure-blob-storage.md#linked-service-properties) (Propiedades del servicio vinculado del blob de Azure) sobre los detalles y las propiedades que se admiten.

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

### <a name="create-an-azure-sql-database-linked-service"></a>Creación de un servicio vinculado a Azure SQL Database

Agregue el código siguiente al método **Main** que crea un **servicio vinculado a Azure SQL Database**. Obtenga más información en la sección [Azure SQL Database linked service properties](connector-azure-sql-database.md#linked-service-properties) (Propiedades del servicio vinculado a Azure SQL Database) sobre los detalles y las propiedades que se admiten.

```csharp
// Create an Azure SQL Database linked service
Console.WriteLine("Creating linked service " + sqlDbLinkedServiceName + "...");

LinkedServiceResource sqlDbLinkedService = new LinkedServiceResource(
    new AzureSqlDatabaseLinkedService
    {
        ConnectionString = new SecureString(azureSqlConnString)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDbLinkedServiceName, sqlDbLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDbLinkedService, client.SerializationSettings));
```

## <a name="create-datasets"></a>Creación de conjuntos de datos

En esta sección, se crean dos conjuntos de datos: uno para el origen y otro para el receptor. 

### <a name="create-a-dataset-for-source-azure-blob"></a>Creación de un conjunto de datos para el blob de Azure de origen

Agregue el código siguiente al método **Main** que crea un **conjunto de datos de blob de Azure**. Obtenga más información en la sección [Azure Blob dataset properties](connector-azure-blob-storage.md#dataset-properties) (Propiedades del conjunto de datos del blob de Azure) sobre los detalles y las propiedades que se admiten.

Se define un conjunto de datos que representa los datos de origen del blob de Azure. Este conjunto de datos de blob hace referencia al servicio vinculado de Azure Storage que creó en el paso anterior y describe:

- La ubicación del blob que se debe copiar de: **FolderPath** y **FileName**;
- El formato de blob que indica cómo analizar el contenido **TextFormat** y su configuración (por ejemplo, el delimitador de columna).
- La estructura de datos, incluidos los tipos de datos y los nombres de columna que, en este caso, se asignan a la tabla SQL del receptor.

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
        FolderPath = inputBlobPath,
        FileName = inputBlobName,
        Format = new TextFormat { ColumnDelimiter = "|" },
        Structure = new List<DatasetDataElement>
        {
            new DatasetDataElement
            {
                Name = "FirstName",
                Type = "String"
            },
            new DatasetDataElement
            {
                Name = "LastName",
                Type = "String"
            }
        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

### <a name="create-a-dataset-for-sink-azure-sql-database"></a>Creación de un conjunto de datos para la instancia Azure SQL Database receptora

Agregue el código siguiente al método **Main** que crea un **conjunto de datos de Azure SQL Database**. Obtenga más información en la sección [Azure SQL Database dataset properties](connector-azure-sql-database.md#dataset-properties) (Propiedades del conjunto de datos de Azure SQL Database) sobre los detalles y las propiedades que se admiten.

Se define un conjunto de datos que representa los datos del receptor de Azure SQL Database. Este conjunto de datos hace referencia al servicio vinculado a Azure SQL Database que creó en el paso anterior. También especifica la tabla SQL que contiene los datos copiados. 

```csharp
// Create a Azure SQL Database dataset
Console.WriteLine("Creating dataset " + sqlDatasetName + "...");
DatasetResource sqlDataset = new DatasetResource(
    new AzureSqlTableDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = sqlDbLinkedServiceName
        },
        TableName = azureSqlTableName
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, sqlDatasetName, sqlDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(sqlDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Crear una canalización

Agregue el código siguiente al método **main** que crea una **canalización con una actividad de copia**. En este tutorial, esta canalización contiene una actividad: la actividad de copia, que toma el conjunto de datos del blob como origen y el conjunto de datos SQL como receptor. Obtenga más información en la sección [Copy Activity Overview](copy-activity-overview.md) (Información general de actividad de copia) sobre los detalles de la actividad de copia.

```csharp
// Create a pipeline with copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToSQL",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = sqlDatasetName
                }
            },
            Source = new BlobSource { },
            Sink = new SqlSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Creación de una ejecución de canalización

Agregue el código siguiente al método **Main** que **desencadena una ejecución de canalización**.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName).Result.Body;
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
    {
        Console.WriteLine(activityRuns.First().Output);
    }
    else
        Console.WriteLine(activityRuns.First().Error);

    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Ejecución del código

Compile e inicie la aplicación y, a continuación, compruebe la ejecución de la canalización.

La consola imprime el progreso de creación de una factoría de datos, un servicio vinculado, conjuntos de datos, una canalización y una ejecución de canalización. A continuación, comprueba el estado de la ejecución de canalización. Espere hasta que vea los detalles de ejecución de actividad con el tamaño de los datos leídos/escritos. A continuación, use herramientas como SSMS (SQL Server Management Studio) o Visual Studio para conectarse a su instancia de Azure SQL Database de destino y compruebe si los datos se copian en la tabla especificada.

### <a name="sample-output"></a>Salida de ejemplo

```json
Creating a data factory AdfV2Tutorial...
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
        "type": "SecureString",
        "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
      }
    }
  }
}
Creating linked service AzureSqlDbLinkedService...
{
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": {
        "type": "SecureString",
        "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": "adfv2tutorial/",
      "fileName": "inputEmp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "|"
      }
    },
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureStorageLinkedService"
    }
  }
}
Creating dataset SqlDataset...
{
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": "dbo.emp"
    },
    "linkedServiceName": {
      "type": "LinkedServiceReference",
      "referenceName": "AzureSqlDbLinkedService"
    }
  }
}
Creating pipeline Adfv2TutorialBlobToSqlCopy...
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
            "type": "SqlSink"
          }
        },
        "inputs": [
          {
            "type": "DatasetReference",
            "referenceName": "BlobDataset"
          }
        ],
        "outputs": [
          {
            "type": "DatasetReference",
            "referenceName": "SqlDataset"
          }
        ],
        "name": "CopyFromBlobToSQL"
      }
    ]
  }
}
Creating pipeline run...
Pipeline run ID: 1cd03653-88a0-4c90-aabc-ae12d843e252
Checking pipeline run status...
Status: InProgress
Status: InProgress
Status: Succeeded
Checking copy activity run details...
{
  "dataRead": 18,
  "dataWritten": 28,
  "rowsCopied": 2,
  "copyDuration": 2,
  "throughput": 0.01,
  "errors": [],
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
  "usedCloudDataMovementUnits": 2,
  "billedDuration": 2
}

Press any key to exit...
```


## <a name="next-steps"></a>Pasos siguientes
La canalización de este ejemplo copia los datos de una ubicación a otra en una instancia de Azure Blob Storage. Ha aprendido a: 

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Creación de servicios vinculados con Azure SQL Database y Azure Storage
> * Creación de conjuntos de datos de Azure SQL Database y el blob de Azure
> * Creación de una canalización que contiene una actividad de copia
> * Inicio de la ejecución de una canalización.
> * Supervisión de las ejecuciones de canalización y actividad.


Pase al tutorial siguiente para obtener información sobre cómo copiar datos desde el entorno local a la nube: 

> [!div class="nextstepaction"]
>[Copiar datos del entorno local a la nube](tutorial-hybrid-copy-powershell.md)
