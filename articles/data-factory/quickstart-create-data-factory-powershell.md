---
title: Copia de datos en Blob Storage mediante Azure Data Factory | Microsoft Docs
description: "Cree una factoría de datos de Azure para copiar datos desde una carpeta de Azure Blob Storage a otra de la misma instancia de Blob Storage."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: hero-article
ms.date: 11/14/2017
ms.author: jingwang
ms.openlocfilehash: 8ee2f48db009da4660a03f91194c4e99f6ecac4a
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2017
---
# <a name="create-an-azure-data-factory-using-powershell"></a>Creación de una factoría de datos de Azure con PowerShell 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versión 2: Versión preliminar](quickstart-create-data-factory-powershell.md)

En esta guía de inicio rápido se describe cómo usar PowerShell para crear una instancia de Azure Data Factory. La canalización que ha creado en esta factoría de datos copia datos de una carpeta a otra en Azure Blob Storage. Para ver un tutorial acerca de cómo transformar datos mediante Azure Data Factory, consulte [Transformación de datos mediante la actividad de Spark en Azure Data Factory](transform-data-using-spark.md). 

En este artículo no se ofrece una introducción detallada al servicio Data Factory. Para ver una introducción al servicio Azure Data Factory, consulte [Introducción al servicio Azure Data Factory](introduction.md).

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Introducción a la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="prerequisites"></a>Requisitos previos

### <a name="azure-subscription"></a>Suscripción de Azure
Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

### <a name="azure-storage-account"></a>Cuenta de Azure Storage
En esta guía de inicio rápido, use una cuenta de Azure Storage (en concreto Blob Storage) de uso general como almacén de datos de **origen** y **receptor o de destino**. Si no dispone de una cuenta de Azure Storage de uso general, consulte el artículo [Creación de una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account), donde se indica cómo crearla. 

#### <a name="get-storage-account-name-and-account-key"></a>Obtener un nombre y una clave de cuenta de Storage
En esta guía de inicio rápido use el nombre y la clave de su cuenta de Azure Storage. El siguiente procedimiento especifica los pasos necesarios para obtener el nombre y la clave de una cuenta de Storage. 

1. Inicie un explorador web y navegue hasta [Azure Portal](https://portal.azure.com). Inicie sesión con su nombre de usuario y contraseña de Azure. 
2. Haga clic en **Más servicios>** en el menú de la izquierda, filtre por la palabra clave **Almacenamiento** y seleccione **Cuentas de almacenamiento**.

    ![Buscar cuenta de Storage](media/quickstart-create-data-factory-powershell/search-storage-account.png)
3. En la lista de cuentas de Storage, filtre por su cuenta de almacenamiento (si fuera necesario) y, después, seleccione **su cuenta de Storage**. 
4. En la página **Cuenta de almacenamiento**, seleccione **Claves de acceso** en el menú.

    ![Obtener nombre y clave de la cuenta de Storage](media/quickstart-create-data-factory-powershell/storage-account-name-key.png)
5. Copie los valores de los campos **Nombre de cuenta de almacenamiento** y **clave1** en el Portapapeles. Péguelos en el Bloc de notas o en cualquier otro editor y guárdelo.  

#### <a name="create-input-folder-and-files"></a>Creación de archivos y carpetas de entrada
En esta sección se crea un contenedor de blobs denominado: adftutorial en Azure Blob Storage. Luego, cree una carpeta denominada: de entrada en el contenedor y, luego, cargar un archivo de ejemplo en la carpeta de entrada. 

1. Instale el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/) si no lo tiene en su equipo. 
2. Inicie el **Explorador de Microsoft Azure Storage** en el equipo.   
3. En la ventana **Conectar a Azure Storage**, seleccione **Use a storage account name and key** (Usar un nombre y una clave de cuenta de almacenamiento) y haga clic en **Siguiente**. Si no ve la ventana **Conectar a Azure Storage**, haga clic con el botón derecho en **Cuentas de almacenamiento** en la vista de árbol y haga clic en **Conectar a Azure Storage**. 

    ![Conectar a Azure Storage](media/quickstart-create-data-factory-powershell/storage-explorer-connect-azure-storage.png)
4. En la ventana **Attach using Name and Key** (Adjuntar mediante nombre y clave), pegue el **nombre de la cuenta** y la **clave de la cuenta** que guardó en el paso anterior. A continuación, haga clic en **Siguiente**. 
5. En la ventana **Resumen de conexiones**, haga clic en **Conectar**.
6. Confirme que ve la cuenta de almacenamiento en la vista de árbol en **(locales y adjuntas)** -> **Cuentas de almacenamiento**. 
7. Expanda **Contenedores de blob** y confirme que la contenedor de blob **adftutorial** no existe. Si ya existe. omita los pasos siguientes para crear el contenedor. 
8. Haga clic con el botón derecho en **Contenedores de blob** y seleccione 
**Crear contenedor de blobs**.

    ![Creación de un contenedor de blobs](media/quickstart-create-data-factory-powershell/stroage-explorer-create-blob-container-menu.png)
9. Escriba **adftutorial** como nombre y presione **ENTRAR**. 
10. Confirme que el contenedor **adftutorial** está seleccionado en la vista de árbol. 
11. Haga clic en **Nueva carpeta** en la barra de herramientas. 

    ![Botón Crear carpeta](media/quickstart-create-data-factory-powershell/stroage-explorer-new-folder-button.png)
12. En la ventana **Crear nuevo directorio virtual**, escriba **input** en **Nombre** y haga clic en **Aceptar**. 

    ![Cuadro de diálogo Crear directorio](media/quickstart-create-data-factory-powershell/storage-explorer-create-new-directory-dialog.png)
13. Inicie el **Bloc de notas** y cree un archivo denominado **emp.txt** con el siguiente contenido: 
    
    ```
    John, Doe
    Jane, Doe
    ```    
    Guárdelo en la carpeta **c:\ADFv2QuickStartPSH**: cree la carpeta **ADFv2QuickStartPSH** si aún no existe. 
14. Haga clic en el botón **Cargar** de la barra de herramientas y seleccione **Cargar archivos**. 

    ![Botón Cargar](media/quickstart-create-data-factory-powershell/storage-explorer-upload-button.png)
15. En la ventana **Cargar archivos**, en **Archivos**, seleccione `...`. 
16. En la ventana **Select folder to upload** (Seleccionar la carpeta que se carga), navegue hasta la carpeta con **emp.txt**y seleccione el archivo. 

    ![Cuadro de diálogo Cargar archivos](media/quickstart-create-data-factory-powershell/storage-explorer-upload-files-dialog.png)
17. En la ventana **Cargar archivos**, haga clic en **Cargar**. 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Azure PowerShell
Instale la versión más reciente de Azure PowerShell si no lo tiene en el equipo. 

1. En el explorador web, navegue hasta la página [Descargue los SDK y herramientas de Azure](https://azure.microsoft.com/downloads/). 
2. Haga clic en **Instalación de Windows** en la sección **Herramientas de línea de comandos** -> **PowerShell**. 
3. Para instalar Azure PowerShell, ejecute el archivo **MSI**. 

Para obtener instrucciones detalladas, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-azure-powershell"></a>Iniciar sesión en Azure PowerShell
Inicie **PowerShell** en su equipo. Mantenga Azure PowerShell abierto hasta el final de esta guía de inicio rápido. Si lo cierra y vuelve a abrirlo, deberá ejecutar los comandos de nuevo.

1. Ejecute el siguiente comando y escriba el nombre de usuario y la contraseña de Azure que utiliza para iniciar sesión en Azure Portal:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Si tiene varias suscripciones de Azure, ejecute el siguiente comando para ver todas las suscripciones de esta cuenta:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Ejecute el comando siguiente para seleccionar la suscripción con la que desea trabajar. Reemplace **SubscriptionId** con el identificador de la suscripción de Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Crear una factoría de datos
1. Defina una variable para el nombre del grupo de recursos que usa en los comandos de PowerShell más adelante. Copie el texto del comando siguiente en PowerShell, especifique el nombre del [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) entre comillas dobles y ejecute el comando. 
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. Defina una variable para el nombre de la factoría de datos que pueda usar en los comandos de PowerShell más adelante. 

    ```powershell
    $dataFactoryName = "<Specify a name for the data factory. It must be globally unique.>";
    ```
1. Defina una variable para la ubicación de la factoría de datos: 

    ```powershell
    $location = "East US"
    ```
4. Para crear el grupo de recursos de Azure, ejecute el comando siguiente: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Si el grupo de recursos ya existe, puede que no desee sobrescribirlo. Asigne otro valor a la variable `$resourceGroupName` e inténtelo de nuevo. Si desea compartir el grupo de recursos con otro usuario, vaya al paso siguiente. 
5. Para crear la factoría de datos, ejecute el siguiente cmdlet, **Set-AzureRmDataFactoryV2**: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Tenga en cuenta los siguientes puntos:

* El nombre del generador de datos de Azure debe ser único global. Si recibe el siguiente error, cambie el nombre y vuelva a intentarlo.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Para crear instancias de Data Factory, hay que ser **colaborador** o **administrador** de la suscripción de Azure.
* Actualmente, la versión 2 de Data Factory permite crear factorías de datos solo en las regiones Este de EE. UU., Este de EE. UU. 2 y Europa Occidental. Los almacenes de datos (Azure Storage, Azure SQL Database, etc.) y los procesos (HDInsight, etc.) que usa la factoría de datos pueden encontrarse en otras regiones.

## <a name="create-a-linked-service"></a>Creación de un servicio vinculado

Cree servicios vinculados en una factoría de datos para vincular los almacenes de datos y los servicios de proceso a la factoría de datos. En esta guía de inicio rápido, basta con crear un servicio vinculado de Azure Storage para usarlo como almacén de origen y receptor. En el ejemplo, se denomina "AzureStorageLinkedService".

1. Cree un archivo JSON llamado **AzureStorageLinkedService.json** en la carpeta **C:\ADFv2QuickStartPSH** con el siguiente contenido: (cree la carpeta ADFv2QuickStartPSH si aún no existe). 

    > [!IMPORTANT]
    > Reemplace &lt;accountName&gt; y &lt;accountKey&gt; por el nombre y la clave de su cuenta de Azure Storage antes de guardar el archivo.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                    "type": "SecureString"
                }
            }
        }
    }
    ```

2. En **Azure PowerShell**, cambie a la carpeta **ADFv2QuickStartPSH**.

3. Ejecute el cmdlet **Set-AzureRmDataFactoryV2LinkedService** para crear el servicio vinculado: **AzureStorageLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -DefinitionFile ".\AzureStorageLinkedService.json"
    ```

    Este es la salida de ejemplo:

    ```
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

## <a name="create-a-dataset"></a>Crear un conjunto de datos

Se define un conjunto de datos que representa los datos que se copian de un origen a un receptor. En este ejemplo, este conjunto de datos de blob hace referencia al servicio vinculado de Azure Storage que creó en el paso anterior. En el conjunto de datos se usa un parámetro cuyo valor se establece en una actividad que consume el conjunto de datos. El parámetro se usa para construir el elemento **folderPath** que apunta a la ubicación de los datos.

1. Cree un archivo JSON llamado **BlobDataset.json** en la carpeta **C:\ADFv2QuickStartPSH** con el siguiente contenido:

    ```json
    {
        "name": "BlobDataset",
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
    ```

2. Para crear el conjunto de datos **BlobDataset**, ejecute el cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "BlobDataset" -DefinitionFile ".\BlobDataset.json"
    ```

    Este es la salida de ejemplo:

    ```
    DatasetName       : BlobDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>Crear una canalización
  
En este ejemplo, esta canalización contiene una actividad y se usan dos parámetros: ruta de acceso de blob de entrada y de salida. Los valores para estos parámetros se establecen cuando se desencadena/ejecuta la canalización. La actividad de copia usa el mismo conjunto de datos de blob creado en el paso anterior como entrada y salida. Cuando se usa el conjunto de datos como conjunto de datos de entrada, se especifica una ruta de acceso de entrada. Cuando se usa el conjunto de datos como conjunto de datos de salida, se especifica una ruta de acceso de salida. 

1. Cree un archivo JSON llamado **Adfv2QuickStartPipeline.json** en la carpeta **C:\ADFv2QuickStartPSH** con el siguiente contenido:

    ```json
    {
        "name": "Adfv2QuickStartPipeline",
        "properties": {
            "activities": [
                {
                    "name": "CopyFromBlobToBlob",
                    "type": "Copy",
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
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    }
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
    ```

2. Para crear la canalización: **Adfv2QuickStartPipeline**, ejecute el cmdlet **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "Adfv2QuickStartPipeline" -DefinitionFile ".\Adfv2QuickStartPipeline.json"
    ```

    Este es la salida de ejemplo:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

## <a name="create-a-pipeline-run"></a>Creación de una ejecución de canalización

En este paso, define los valores para los parámetros de canalización: **inputPath** y **outputPath** con valores reales de rutas de acceso de blob de origen y receptor. A continuación, use estos argumentos para crear una canalización. 

1. Cree un archivo JSON llamado **BlobDataset.json** en la carpeta **C:\ADFv2QuickStartPSH** con el siguiente contenido:

    Si va a usar distintos contenedores y carpetas, reemplace el valor de **inputPath** y **outputPath** por la ruta de acceso del blob de origen y del receptor.

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
    }
    ```

2. Ejecute el cmdlet **Invoke-AzureRmDataFactoryV2Pipeline** para crear una ejecución de canalización y pasar los valores de parámetro. También se captura el id. de ejecución de canalización para la futura supervisión.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-a-pipeline-run"></a>Supervisar una ejecución de canalización

1. Ejecute el script siguiente para comprobar continuamente el estado de ejecución de la canalización hasta que termine de copiar los datos.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```

    Esta es la salida de ejemplo de la ejecución de canalización:

    ```
    Pipeline is running...status: InProgress
    Pipeline run finished. The status is:  Succeeded
    
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    RunId             : 0000000000-0000-0000-0000-0000000000000
    PipelineName      : Adfv2QuickStartPipeline
    LastUpdated       : 9/28/2017 8:28:38 PM
    Parameters        : {[inputPath, adftutorial/input], [outputPath, adftutorial/output]}
    RunStart          : 9/28/2017 8:28:14 PM
    RunEnd            : 9/28/2017 8:28:38 PM
    DurationInMs      : 24151
    Status            : Succeeded
    Message           :
    ```

2. Ejecute el script siguiente para recuperar detalles de la ejecución de la actividad de copia, como el tamaño de los datos leídos o escritos.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```
3. Confirme que ha obtenido una salida similar a la siguiente salida de ejemplo de resultado de ejecución de actividad:

    ```json
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : SPTestFactory0928
    ActivityName      : CopyFromBlobToBlob
    PipelineRunId     : 00000000000-0000-0000-0000-000000000000
    PipelineName      : Adfv2QuickStartPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, copyDuration, throughput...}
    LinkedServiceName :
    ActivityRunStart  : 9/28/2017 8:28:18 PM
    ActivityRunEnd    : 9/28/2017 8:28:36 PM
    DurationInMs      : 18095
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity 'Output' section:
    "dataRead": 38
    "dataWritten": 38
    "copyDuration": 7
    "throughput": 0.01
    "errors": []
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)"
    "usedCloudDataMovementUnits": 2
    "billedDuration": 14
    ```

## <a name="verify-the-output"></a>Comprobación del resultado
El conducto crea automáticamente la carpeta de salida en el contenedor de blob adftutorial. A continuación, copia el archivo emp.txt de la carpeta de entrada a la carpeta de salida. Use el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/) para comprobar que los blobs de inputBlobPath se copian en outputBlobPath. 

## <a name="clean-up-resources"></a>Limpieza de recursos
Hay dos forma de eliminar los recursos que ha creado en la guía de inicio rápido. Puede eliminar el [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md), lo que incluye todos los recursos del grupo de recursos. Si quiere mantener intacto el resto de recursos, elimine solo la factoría de datos que creó en este tutorial.

Ejecute el comando siguiente para eliminar el grupo de recursos completo: 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Ejecute el comando siguiente para eliminar solo la factoría de datos: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Pasos siguientes
La canalización de este ejemplo copia los datos de una ubicación a otra en una instancia de Azure Blob Storage. Consulte los [tutoriales](tutorial-copy-data-dot-net.md) para obtener información acerca del uso de Data Factory en otros escenarios. 
