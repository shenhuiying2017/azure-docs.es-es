---
title: Copia de datos del entorno local a la nube con Azure Data Factory | Microsoft Docs
description: "Averigüe cómo puede copiar datos desde un almacén de datos local a la nube de Azure mediante el uso del entorno de ejecución de integración autohospedado en Azure Data Factory."
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
ms.date: 09/14/2017
ms.author: jingwang
ms.openlocfilehash: 74e2a57aa933c7025db952fa09de236f5dabb8c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-between-on-premises-and-cloud"></a>Copia de datos entre el entorno local y la nube
Azure Data Factory es un servicio de integración de datos basado en la nube que le permite crear flujos de trabajo basados en datos en la nube a fin de coordinar y automatizar el movimiento y la transformación de datos. Mediante Azure Data Factory, puede crear y programar flujos de trabajo orientados a datos (llamados canalizaciones) que pueden ingerir datos de almacenes de datos dispares, procesar o transformar los datos mediante servicios de proceso, como Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics y Azure Machine Learning, y publicar datos de salida en almacenes de datos, como Azure SQL Data Warehouse para que los consuman las aplicaciones de inteligencia empresarial (BI). 

En este tutorial, usará Azure PowerShell para crear una canalización de Data Factory que copia los datos de una base de datos de SQL Server local a Azure Blob Storage. Deberá crear y usar una instancia de Integration Runtime (IR) autohospedada de Azure Data Factory, que permite la integración de almacenes de datos locales y almacenes de datos en la nube.  Para obtener información sobre el uso de otras herramientas o SDK para crear una factoría de datos, consulte la sección [Inicios rápidos](quickstart-create-data-factory-dot-net.md). 

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Creación de una factoría de datos
> * Creación de una instancia de Integration Runtime autohospedada
> * Creación y cifrado del servicio vinculado de SQL Server local en la instancia de Integration Runtime autohospedada
> * Creación de un servicio vinculado de Azure Storage
> * Creación de conjuntos de datos de SQL Server y Azure Storage
> * Creación de una canalización con una actividad de copia para mover los datos.
> * Inicio de la ejecución de una canalización
> * Supervisión de la ejecución de canalización y actividad

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

* **SQL Server**. Use una base de datos de SQL Server local como almacén de datos de **origen** en este tutorial. 
* **Cuenta de Almacenamiento de Azure**. Azure Blob Storage se usará como un almacén de datos de **destino o receptor** en este tutorial. Si no tiene una cuenta de almacenamiento de Azure, consulte la sección [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para ver los pasos para su creación.
* **Azure PowerShell**. Siga las instrucciones de [Instalación y configuración de Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Crear una factoría de datos

1. Inicie **PowerShell**. Mantenga Azure PowerShell abierto hasta el final de este tutorial. Si lo cierra y vuelve a abrirlo, deberá ejecutar los comandos de nuevo.

    Ejecute el siguiente comando y escriba el nombre de usuario y la contraseña que utiliza para iniciar sesión en Azure Portal:
    ```powershell
    Login-AzureRmAccount
    ```
    Ejecute el siguiente comando para ver todas las suscripciones de esta cuenta:

    ```powershell
    Get-AzureRmSubscription
    ```
    Ejecute el comando siguiente para seleccionar la suscripción con la que desea trabajar. Reemplace **SubscriptionId** con el identificador de la suscripción de Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Ejecute el cmdlet **AzureRmDataFactoryV2 Set** para crear una factoría de datos. Reemplace los marcadores de posición con sus propios valores antes de ejecutar el comando.

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $df = Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Tenga en cuenta los siguientes puntos:

    * El nombre del generador de datos de Azure debe ser único global. Si recibe el siguiente error, cambie el nombre y vuelva a intentarlo.

        ```
        Data factory name "<data factory name>" is not available.
        ```

    * Para crear instancias de Data Factory, debe ser administrador o colaborador de la suscripción de Azure.
    * Actualmente, Data Factory le permite crear una factoría de datos solo en la región Este de EE. UU. y Este de EE. UU. 2. Los almacenes de datos (Azure Storage, Azure SQL Database, etc.) y los procesos (HDInsight, etc.) que usa la factoría de datos pueden encontrarse en otras regiones.

## <a name="create-a-self-hosted-ir"></a>Creación de una instancia de IR autohospedada

En esta sección, puede crear una instancia de Integration Runtime autohospedada y asociarla a un nodo local (máquina).

1. Cree una instancia de Integration Runtime autohospedada. Use un nombre único en caso de que exista otra instancia de Integration Runtime con el mismo nombre.

   ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Este es la salida de ejemplo:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. Ejecute el comando siguiente para recuperar el estado de la instancia de Integration Runtime creada.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Este es la salida de ejemplo:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption : 
   Version                   : 
   Capabilities              : {}
   ScheduledUpdateDate       : 
   UpdateDelayOffset         : 
   LocalTimeZoneOffset       : 
   AutoUpdate                : 
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. Ejecute el siguiente comando para recuperar las claves de autenticación para registrar la instancia de Integration Runtime autohospedada con el servicio Data Factory en la nube. Copie una de las claves para registrar la instancia de Integration Runtime autohospedada.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Este es la salida de ejemplo: 

   ```json
   {
       "AuthKey1":  "IR@8437c862-d6a9-4fb3-87dd-7d4865a9e845@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@8437c862-d6a9-4fb3-85dd-7d4865a9e845@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

4. [Descargue](https://www.microsoft.com/download/details.aspx?id=39717) la instancia de Integration Runtime autohospedada en una máquina Windows local y use la clave de autenticación que obtuvo en el paso anterior para registrar manualmente la instancia de Integration Runtime autohospedada. 

   ![Registro de Integration Runtime](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

   Verá el siguiente mensaje cuando la instancia de Integration Runtime autohospedada se haya registrado correctamente: 

   ![Se registró correctamente](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

   Verá la siguiente página cuando el nodo esté conectado al servicio en la nube: 
    
   ![El nodo está conectado](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>Crear servicios vinculados 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Creación de un servicio vinculado a Azure Storage (destino/receptor)

1. Cree un archivo JSON con el nombre **AzureStorageLinkedService.json** en la carpeta **C:\ADFv2Tutorial** con el siguiente contenido: Si todavía no existe, cree la carpeta ADFv2Tutorial.  Reemplace &lt;accountname&gt; y &lt;accountkey&gt; por el nombre y la clave de su cuenta de Azure Storage.

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": { 
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

2. En **Azure PowerShell**, cambie a la carpeta **ADFv2Tutorial**.

   Ejecute el cmdlet **Set-AzureRmDataFactoryV2LinkedService** para crear el servicio vinculado: **AzureStorageLinkedService**. Los cmdlets usados en este tutorial toman valores para los parámetros **ResourceGroupName** y **DataFactoryName**. Como alternativa, puede pasar el objeto **DataFactory** devuelto por el cmdlet Set-AzureRmDataFactoryV2 sin escribir ResourceGroupName ni DataFactoryName cada vez que ejecute un cmdlet.

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Este es una salida de ejemplo:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Creación y cifrado de un servicio vinculado de SQL Server (origen)

1. Cree un archivo JSON denominado **SqlServerLinkedService.json** en la carpeta **C:\ADFv2Tutorial** con el siguiente contenido: reemplace **&lt;servername>**, **&lt;databasename>**, **&lt;username>**, **&lt;servername>** y **&lt;password>** por valores de su instancia de SQL Server antes de guardar el archivo. Reemplace **&lt;integration** **runtime** **name>** por el nombre de su instancia de Integration Runtime. 

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```
2. Para cifrar los datos confidenciales de la carga de JSON en la instancia de Integration Runtime autohospedada local, podemos ejecutar **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** y pasar la carga. Este cifrado garantiza que las credenciales se cifren mediante la interfaz de programación de aplicaciones (API) de protección de datos y se almacenen en el nodo de Integration Runtime autohospedado localmente. La carga de salida se puede redirigir a otro archivo JSON (en este caso, "encryptedLinkedService.json"), que contiene las credenciales cifradas. 

    Reemplace **&lt;integration runtime name&gt;** por el nombre de su instancia de Integration Runtime antes de ejecutar el comando.

   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName <integration runtime name> -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Ejecute el siguiente comando con JSON en el paso anterior para crear **SqlServerLinkedService**:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json" 
   ```


## <a name="create-datasets"></a>Creación de conjuntos de datos

### <a name="prepare-an-on-premises-sql-server-for-the-tutorial"></a>Preparación de una instancia local de SQL Server para el tutorial

En este paso, creará conjuntos de datos de entrada y de salida que representan datos de entrada y de salida para la operación de copia (base de datos de SQL Server local => Azure Blob Storage). Antes de crear los conjuntos de datos, realice estos pasos (los pasos detallados se indican después de la lista):

- Cree una tabla con el nombre **emp** en la base de datos de SQL Server que agregó como servicio vinculado a la factoría de datos e inserte un par de entradas de ejemplo en la tabla.
- Cree un contenedor de blobs llamado **adftutorial** en la cuenta de Azure Blob Storage que agregó como un servicio vinculado a la factoría de datos.


1. En la base de datos que especificó para el servicio vinculado de SQL Server local (**SqlServerLinkedService**), use el siguiente script de SQL para crear la tabla **emp** en la base de datos.

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Inserte algún ejemplo en la tabla:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```



### <a name="create-a-dataset-for-source-sql-database"></a>Creación de un conjunto de datos de la instancia de SQL Database de origen

1. Cree un archivo JSON denominado **SqlServerDataset.json** en la carpeta **C:\ADFv2Tutorial** con el siguiente contenido:  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
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
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. Para crear el conjunto de datos **SqlServerDataset**, ejecute el cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Este es la salida de ejemplo:

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>Creación de un conjunto de datos para la instancia de Azure Blob Storage receptora

1. Cree un archivo JSON denominado **AzureBlobDataset.json** en la carpeta **C:\ADFv2Tutorial** con el siguiente contenido: 

    > [!IMPORTANT]
    > Con este código de ejemplo se da por supuesto que tiene un contenedor denominado **adftutorial** en Azure Blob Storage.

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. Para crear el conjunto de datos **AzureBlobDataset**, ejecute el cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Este es la salida de ejemplo:

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-pipelines"></a>Creación de canalizaciones

### <a name="create-the-pipeline-sqlservertoblobpipeline"></a>Creación de la canalización "SqlServerToBlobPipeline"

1. Cree un archivo JSON denominado **SqlServerToBlobPipeline.json** en la carpeta **C:\ADFv2Tutorial** con el siguiente contenido:

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Para crear la canalización **SQLServerToBlobPipeline**, ejecute el cmdlet **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Este es la salida de ejemplo:

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```



## <a name="start-and-monitor-a-pipeline-run"></a>Inicio y supervisión de una ejecución de la canalización

1. Inicie una ejecución para la canalización "SQLServerToBlobPipeline" y capture el identificador de ejecución de la canalización a fin de poder realizar una supervisión en el futuro.  

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
    ```

2. Ejecute el siguiente script para comprobar continuamente el estado de ejecución de la canalización "**SQLServerToBlobPipeline**" e imprimir el resultado final.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    } 
    ```

    Este es el resultado de la ejecución de ejemplo:

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName : 
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Puede obtener el identificador de ejecución de la canalización "**SQLServerToBlobPipeline**" y comprobar el resultado de la ejecución de actividad detallado como se muestra a continuación.

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Este es el resultado de la ejecución de ejemplo:

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 4,
      "throughput": 0.01,
      "errors": []
    }
    ```
4. Conéctese a su instancia de Azure Blob Storage receptora y confirme que los datos se han copiado correctamente desde Azure SQL Database.

## <a name="next-steps"></a>Pasos siguientes
La canalización de este ejemplo copia los datos de una ubicación a otra en una instancia de Azure Blob Storage. Ha aprendido a: 

> [!div class="checklist"]
> * Creación de una factoría de datos.
> * Crear de una instancia de Integration Runtime autohospedada
> * Creación y cifrado del servicio vinculado de SQL Server local en la instancia de Integration Runtime autohospedada
> * Creación de un servicio vinculado de Azure Storage
> * Creación de conjuntos de datos de SQL Server y Azure Storage
> * Creación de una canalización con una actividad de copia para mover los datos.
> * Inicio de la ejecución de una canalización
> * Supervisión de la ejecución de canalización y actividad

Consulte la sección [supported data stores](copy-activity-overview.md#supported-data-stores-and-formats) (Almacenes de datos compatibles) para ver la lista de almacenes de datos que admite Azure Data Factory como orígenes y receptores.

Vaya al tutorial siguiente para obtener información sobre cómo copiar datos de forma masiva de un origen a un destino:

> [!div class="nextstepaction"]
>[Copiar datos de forma masiva](tutorial-bulk-copy.md)
