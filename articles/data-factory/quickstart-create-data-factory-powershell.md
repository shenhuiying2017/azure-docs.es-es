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
ms.date: 11/16/2017
ms.author: jingwang
ms.openlocfilehash: 254dcb6642afc19f434df837c9073d2dd7314313
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/20/2017
---
# <a name="create-an-azure-data-factory-using-powershell"></a>Creación de una factoría de datos de Azure con PowerShell 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versión 2: Versión preliminar](quickstart-create-data-factory-powershell.md)

En esta guía de inicio rápido se describe cómo usar PowerShell para crear una instancia de Azure Data Factory. La canalización que ha creado en esta factoría de datos **copia** los datos de una carpeta a otra en Azure Blob Storage. Para ver un tutorial acerca de cómo **transformar** datos mediante Azure Data Factory, consulte [Transformación de datos en la nube mediante la actividad de Spark en Azure Data Factory](transform-data-using-spark.md). 

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Introducción a la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> En este artículo no se ofrece una introducción detallada al servicio Data Factory. Para ver una introducción al servicio Azure Data Factory, consulte [Introducción al servicio Azure Data Factory](introduction.md).

## <a name="prerequisites"></a>Requisitos previos

### <a name="azure-subscription"></a>Suscripción de Azure
Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

### <a name="azure-roles"></a>Roles de Azure
Para crear instancias de Data Factory, la cuenta de usuario que use para iniciar sesión en Azure debe ser un miembro de los roles **colaborador** o **propietario**, o de **administrador** de la suscripción de Azure. En Azure Portal, haga clic en el **nombre de usuario**, en la esquina superior derecha, y seleccione **Permisos** para ver los permisos que tiene en la suscripción. Si tiene acceso a varias suscripciones, elija la correspondiente. Para obtener instrucciones de ejemplo sobre cómo agregar un usuario a un rol, consulte el artículo sobre la [adición de roles](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="azure-storage-account"></a>Cuenta de Azure Storage
En esta guía de inicio rápido, use una cuenta de Azure Storage (en concreto Blob Storage) de uso general como almacén de datos de **origen** y **destino**. Si no dispone de una cuenta de Azure Storage de uso general, consulte el artículo [Creación de una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account), donde se indica cómo crearla. 

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
En esta sección se crea un contenedor de blobs denominado **adftutorial** en la instancia de Azure Blob Storage. Luego, cree una carpeta denominada **entrada** en el contenedor y cargue un archivo de ejemplo en la carpeta de entrada. 

1. En la página **Cuenta de almacenamiento**, cambie a **Información general** y, a continuación, haga clic en **Blobs**. 

    ![Selección de la opción Blobs](media/quickstart-create-data-factory-powershell/select-blobs.png)
2. En la página **Blob service**, haga clic en **+ Contenedor** en la barra de herramientas. 

    ![Botón Agregar contenedor](media/quickstart-create-data-factory-powershell/add-container-button.png)    
3. En el cuadro de diálogo **Nuevo contenedor**, escriba **adftutorial** para el nombre y haga clic en **Aceptar**. 

    ![Especificación de un nombre de contenedor](media/quickstart-create-data-factory-powershell/new-container-dialog.png)
4. Haga clic en **adftutorial** en la lista de contenedores. 

    ![Selección del contenedor](media/quickstart-create-data-factory-powershell/seelct-adftutorial-container.png)
1. En la página **Contenedor**, haga clic en **Cargar** en la barra de herramientas.  

    ![Botón Cargar](media/quickstart-create-data-factory-powershell/upload-toolbar-button.png)
6. En la página **Cargar blob**, haga clic en **Avanzado**.

    ![Hacer clic en el vínculo Avanzado](media/quickstart-create-data-factory-powershell/upload-blob-advanced.png)
7. Inicie **Bloc de notas** y cree un archivo denominado **emp.txt** con el contenido siguiente. Luego guárdelo en la carpeta **c:\ADFv2QuickStartPSH**. Cree la carpeta **ADFv2QuickStartPSH** si aún no existe.
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. En Azure Portal, en la página **Cargar blob**, busque y seleccione el archivo **emp.txt** para el campo **Archivos**. 
9. Escriba **entrada** como valor de **Cargar en carpeta**. 

    ![Carga de la configuración del blob](media/quickstart-create-data-factory-powershell/upload-blob-settings.png)    
10. Confirme que la carpeta es **entrada** y el archivo es **emp.txt**, y haga clic en **Cargar**.
11. Debería ver el archivo **emp.txt** y el estado de la carga en la lista. 
12. Cierre la página **Cargar blob** haciendo clic en **X**, en la esquina. 

    ![Cierre de la página de carga del blob](media/quickstart-create-data-factory-powershell/close-upload-blob.png)
1. Mantenga abierta la página **Contenedor**. Úsela para comprobar la salida al final de esta guía de inicio rápido. 

### <a name="azure-powershell"></a>Azure PowerShell

#### <a name="install-azure-powershell"></a>Azure PowerShell
Instale la versión más reciente de Azure PowerShell si no lo tiene en el equipo. 

1. En el explorador web, navegue hasta la página [Descargue los SDK y herramientas de Azure](https://azure.microsoft.com/downloads/). 
2. Haga clic en **Instalación de Windows** en la sección **Herramientas de línea de comandos** -> **PowerShell**. 
3. Para instalar Azure PowerShell, ejecute el archivo **MSI**. 

Para obtener instrucciones detalladas, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-azure-powershell"></a>Iniciar sesión en Azure PowerShell

1. Inicie **PowerShell** en su equipo. Mantenga Azure PowerShell abierto hasta el final de esta guía de inicio rápido. Si lo cierra y vuelve a abrirlo, deberá ejecutar los comandos de nuevo.

    ![Inicie PowerShell](media/quickstart-create-data-factory-powershell/search-powershell.png)
1. Ejecute el siguiente comando y escriba los mismos nombre de usuario y contraseña de Azure que utiliza para iniciar sesión en Azure Portal:
       
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
1. Defina una variable para el nombre del grupo de recursos que usa en los comandos de PowerShell más adelante. Copie el texto del comando siguiente en PowerShell, especifique el nombre del [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) entre comillas dobles y ejecute el comando. Por ejemplo: `"adfrg"`.
   
     ```powershell
    $resourceGroupName = "<Specify a name for the Azure resource group>";
    ```
2. Defina una variable para el nombre de la factoría de datos. 

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
    Si el grupo de recursos ya existe, puede que no desee sobrescribirlo. Asigne otro valor a la variable `$resourceGroupName` y ejecute el comando de nuevo. 
5. Para crear la factoría de datos, ejecute el siguiente cmdlet, **Set-AzureRmDataFactoryV2**: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Tenga en cuenta los siguientes puntos:

* El nombre del generador de datos de Azure debe ser único global. Si recibe el siguiente error, cambie el nombre y vuelva a intentarlo.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Para crear instancias de Data Factory, la cuenta de usuario que use para iniciar sesión en Azure debe ser un miembro de los roles **colaborador** o **propietario**, o de **administrador** de la suscripción de Azure.
* Actualmente, la versión 2 de Data Factory permite crear factorías de datos solo en las regiones Este de EE. UU., Este de EE. UU. 2 y Europa Occidental. Los almacenes de datos (Azure Storage, Azure SQL Database, etc.) y los procesos (HDInsight, etc.) que usa la factoría de datos pueden encontrarse en otras regiones.

## <a name="create-a-linked-service"></a>Creación de un servicio vinculado

Cree servicios vinculados en una factoría de datos para vincular los almacenes de datos y los servicios de proceso a la factoría de datos. En esta guía de inicio rápido, creará un servicio vinculado de Azure Storage que se utiliza para ambos almacenes: de origen y de recepción. El servicio vinculado tiene la información de conexión que usa el servicio Data Factory en el entorno de tiempo de ejecución para conectarse a él.

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
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net",
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
En este paso, define un conjunto de datos que representa los datos que se copian de un origen a un receptor. El conjunto de datos es de tipo **AzureBlob**. Hace referencia al **servicio vinculado de Azure Storage** que creó en el paso anterior. Toma un parámetro para construir la propiedad **folderPath**. Para un conjunto de datos de entrada, la actividad de copia de la canalización pasa la ruta de acceso de entrada como un valor para este parámetro. De forma similar, para un conjunto de datos de salida, la actividad de copia pasa la ruta de acceso de salida como un valor para este parámetro. 

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
  
En esta guía de inicio rápido, creará una canalización con una actividad que admita dos parámetros: ruta de acceso de blob de entrada y de salida. Los valores para estos parámetros se establecen cuando se desencadena/ejecuta la canalización. La actividad de copia usa el mismo conjunto de datos de blob creado en el paso anterior como entrada y salida. Cuando se usa el conjunto de datos como conjunto de datos de entrada, se especifica una ruta de acceso de entrada. Cuando se usa el conjunto de datos como conjunto de datos de salida, se especifica una ruta de acceso de salida. 

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

    ```json
    {
        "inputPath": "adftutorial/input",
        "outputPath": "adftutorial/output"
    }
    ```
2. Ejecute el cmdlet **Invoke-AzureRmDataFactoryV2Pipeline** para crear una ejecución de canalización y pasar los valores de parámetro. El cmdlet devuelve el identificador de ejecución de la canalización para realizar una supervisión en un futuro.

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
    ```

## <a name="monitor-the-pipeline-run"></a>Supervisión de la ejecución de la canalización

1. Ejecute el script de PowerShell siguiente para comprobar continuamente el estado de ejecución de la canalización hasta que termine de copiar los datos. Copie y pegue el siguiente script en la ventana de PowerShell y presione ENTRAR. 

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

        Start-Sleep -Seconds 10
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

    Si ve el error:
    ```
    Activity CopyFromBlobToBlob failed: Failed to detect region of linked service 'AzureStorage' : 'AzureStorageLinkedService' with error '[Region Resolver] Azure Storage failed to get address for DNS. Warning: System.Net.Sockets.SocketException (0x80004005): No such host is known
    ```
    siga estos pasos: 
    1. En el archivo AzureStorageLinkedService.json, confirme que el nombre y la clave de la cuenta de Azure Storage son correctos. 
    2. Compruebe que el formato de la cadena de conexión es correcto. Las propiedades, por ejemplo, AccountName y AccountKey, están separadas por el carácter de punto y coma (`;`). 
    3. Si el nombre y la clave de la cuenta están entre corchetes, quítelos. 
    4. A continuación, se muestra una cadena de conexión de ejemplo: 

        ```json
        "connectionString": {
            "value": "DefaultEndpointsProtocol=https;AccountName=mystorageaccountname;AccountKey=mystorageacountkey;EndpointSuffix=core.windows.net",
            "type": "SecureString"
        }
        ```
    5. Vuelva a crear el servicio vinculado siguiendo los pasos de la sección [Creación de un servicio vinculado](#create-a-linked-service). 
    6. Vuelva a ejecutar la canalización siguiendo los pasos de la sección [Creación de una ejecución de canalización ](#create-a-pipeline-run). 
    7. Ejecute el comando de supervisión actual para supervisar la nueva ejecución de canalización. 
1. Ejecute el script siguiente para recuperar detalles de la ejecución de la actividad de copia, como el tamaño de los datos leídos o escritos.

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
El conducto crea automáticamente la carpeta de salida en el contenedor de blob adftutorial. A continuación, copia el archivo emp.txt de la carpeta de entrada a la carpeta de salida. 

1. En Azure Portal, en la página de contenedor **adftutorial**, haga clic en **Actualizar** para ver la carpeta de salida. 
    
    ![Actualizar](media/quickstart-create-data-factory-powershell/output-refresh.png)
2. Haga clic en **output** en la lista de carpetas. 
2. Confirme que **emp.txt** se ha copiado en la carpeta de salida. 

    ![Actualizar](media/quickstart-create-data-factory-powershell/output-file.png)

## <a name="clean-up-resources"></a>Limpieza de recursos
Hay dos forma de eliminar los recursos que ha creado en la guía de inicio rápido. Puede eliminar el [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md), lo que incluye todos los recursos del grupo de recursos. Si quiere mantener intacto el resto de recursos, elimine solo la factoría de datos que creó en este tutorial.

Al eliminar un grupo de recursos, se eliminan todos los recursos incluidas las factorías de datos. Ejecute el comando siguiente para eliminar el grupo de recursos completo: 
```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

Si desea eliminar solo la factoría de datos, no el grupo de recursos completo, ejecute el siguiente comando: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Pasos siguientes
La canalización de este ejemplo copia los datos de una ubicación a otra en una instancia de Azure Blob Storage. Consulte los [tutoriales](tutorial-copy-data-dot-net.md) para obtener información acerca del uso de Data Factory en otros escenarios. 
