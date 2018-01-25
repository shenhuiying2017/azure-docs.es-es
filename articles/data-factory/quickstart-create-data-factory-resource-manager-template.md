---
title: "Creación de una instancia de Azure Data Factory mediante una plantilla de Resource Manager | Microsoft Docs"
description: "En este tutorial, creará una canalización de Azure Data Factory de ejemplo con la plantilla de Azure Resource Manager."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: spelluru
ms.openlocfilehash: 5008d8f0ce36f09ff26bf10fadbf14da6b474ea9
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/25/2018
---
# <a name="tutorial-create-an-azure-data-factory-using-azure-resource-manager-template"></a>Tutorial: Creación de una instancia de Azure Data Factory mediante una plantilla de Azure Resource Manager
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Versión 2: versión preliminar](quickstart-create-data-factory-resource-manager-template.md) 

En esta guía de inicio rápido se describe cómo usar una plantilla de Azure Resource Manager para crear una instancia de Azure Data Factory. La canalización que ha creado en esta factoría de datos **copia** los datos de una carpeta a otra en Azure Blob Storage. Para ver un tutorial acerca de cómo **transformar** datos mediante Azure Data Factory, consulte [Transformación de datos en la nube mediante la actividad de Spark en Azure Data Factory](transform-data-using-spark.md). 

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Tutorial: Compilación de la primera Data Factory de Azure con la plantilla de Azure Resource Manager](v1/data-factory-build-your-first-pipeline-using-arm.md).
>
> En este artículo no se ofrece una introducción detallada al servicio Data Factory. Para ver una introducción al servicio Azure Data Factory, consulte [Introducción al servicio Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="azure-powershell"></a>Azure PowerShell
Instale los módulos de Azure PowerShell siguiendo las instrucciones de [Cómo instalar y configurar Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="resource-manager-templates"></a>Plantillas de Resource Manager
Para más información acerca de las plantillas de Azure Resource Manager en general, consulte [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). 

En la siguiente sección se proporciona la plantilla completa de Resource Manager para definir las entidades de Data Factory para que pueda ejecutar el tutorial rápidamente y probar la plantilla. Para entender cómo se define cada entidad de Data Factory, consulte la sección [Entidades de Data Factory en la plantilla](#data-factory-entities-in-the-template).

## <a name="data-factory-json"></a>JSON de Data Factory 
Cree un archivo JSON llamado **ADFTutorialARM.json** en la carpeta **C:\ADFTutorial** con el siguiente contenido:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": {
        "dataFactoryName": {
            "type": "string",
            "metadata": {
                "description": "Name of the data factory. Must be globally unique."
            }
        },
        "dataFactoryLocation": {
            "type": "string",
            "allowedValues": [
                "East US",
                "East US 2",
                "West Europe"
            ],
            "defaultValue": "East US",
            "metadata": {
                "description": "Location of the data factory. Currently, only East US, East US 2, and West Europe are supported. "
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Azure storage account that contains the input/output data."
            }
        },
        "storageAccountKey": {
            "type": "securestring",
            "metadata": {
                "description": "Key for the Azure storage account."
            }
        },
        "blobContainer": {
            "type": "string",
            "metadata": {
                "description": "Name of the blob container in the Azure Storage account."
            }
        },
        "inputBlobFolder": {
            "type": "string",
            "metadata": {
                "description": "The folder in the blob container that has the input file."
            }
        },
        "inputBlobName": {
            "type": "string",
            "metadata": {
                "description": "Name of the input file/blob."
            }
        },
        "outputBlobFolder": {
            "type": "string",
            "metadata": {
                "description": "The folder in the blob container that will hold the transformed data."
            }
        },
        "outputBlobName": {
            "type": "string",
            "metadata": {
                "description": "Name of the output file/blob."
            }
        },
        "triggerStartTime": {
            "type": "string",
            "metadata": {
                "description": "Start time for the trigger."
            }
        },
        "triggerEndTime": {
            "type": "string",
            "metadata": {
                "description": "End time for the trigger."
            }
        }
    },
    "variables": {
        "azureStorageLinkedServiceName": "ArmtemplateStorageLinkedService",
        "inputDatasetName": "ArmtemplateTestDatasetIn",
        "outputDatasetName": "ArmtemplateTestDatasetOut",
        "pipelineName": "ArmtemplateSampleCopyPipeline",
        "triggerName": "ArmTemplateTestTrigger"
    },
    "resources": [{
        "name": "[parameters('dataFactoryName')]",
        "apiVersion": "2017-09-01-preview",
        "type": "Microsoft.DataFactory/factories",
        "location": "[parameters('dataFactoryLocation')]",
        "properties": {
            "loggingStorageAccountName": "[parameters('storageAccountName')]",
            "loggingStorageAccountKey": "[parameters('storageAccountKey')]"
        },
        "resources": [{
                "type": "linkedservices",
                "name": "[variables('azureStorageLinkedServiceName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "AzureStorage",
                    "description": "Azure Storage linked service",
                    "typeProperties": {
                        "connectionString": {
                            "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]",
                            "type": "SecureString"
                        }
                    }
                }
            },
            {
                "type": "datasets",
                "name": "[variables('inputDatasetName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "AzureBlob",
                    "typeProperties": {
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'), '/')]",
                        "fileName": "[parameters('inputBlobName')]"
                    },
                    "linkedServiceName": {
                        "referenceName": "[variables('azureStorageLinkedServiceName')]",
                        "type": "LinkedServiceReference"
                    }
                }
            },
            {
                "type": "datasets",
                "name": "[variables('outputDatasetName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "AzureBlob",
                    "typeProperties": {
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'), '/')]",
                        "fileName": "[parameters('outputBlobName')]"
                    },
                    "linkedServiceName": {
                        "referenceName": "[variables('azureStorageLinkedServiceName')]",
                        "type": "LinkedServiceReference"
                    }
                }
            },
            {
                "type": "pipelines",
                "name": "[variables('pipelineName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]",
                    "[variables('inputDatasetName')]",
                    "[variables('outputDatasetName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "activities": [{
                        "type": "Copy",
                        "typeProperties": {
                            "source": {
                                "type": "BlobSource"
                            },
                            "sink": {
                                "type": "BlobSink"
                            }
                        },
                        "name": "MyCopyActivity",
                        "inputs": [{
                            "referenceName": "[variables('inputDatasetName')]",
                            "type": "DatasetReference"
                        }],
                        "outputs": [{
                            "referenceName": "[variables('outputDatasetName')]",
                            "type": "DatasetReference"
                        }]
                    }]
                }
            },
            {
                "type": "triggers",
                "name": "[variables('triggerName')]",
                "dependsOn": [
                    "[parameters('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]",
                    "[variables('inputDatasetName')]",
                    "[variables('outputDatasetName')]",
                    "[variables('pipelineName')]"
                ],
                "apiVersion": "2017-09-01-preview",
                "properties": {
                    "type": "ScheduleTrigger",
                    "typeProperties": {
                        "recurrence": {
                            "frequency": "Hour",
                            "interval": 1,
                            "startTime": "[parameters('triggerStartTime')]",
                            "endTime": "[parameters('triggerEndTime')]",
                            "timeZone": "UTC"
                        }
                    },
                    "pipelines": [{
                        "pipelineReference": {
                            "type": "PipelineReference",
                            "referenceName": "ArmtemplateSampleCopyPipeline"
                        },
                        "parameters": {}
                    }]
                }
            }
        ]
    }]
}
```

## <a name="parameters-json"></a>Parámetros JSON
Cree un archivo JSON denominado **ADFTutorialARM Parameters.json** que contenga parámetros para la plantilla de Azure Resource Manager.  

> [!IMPORTANT]
> - Especifique el nombre y la clave de la cuenta de Azure Storage para los parámetros **storageAccountName** y **storageAccountKey** en este archivo de parámetros. Ha creado el contenedor adftutorial y ha cargado el archivo de ejemplo (emp.txt) en la carpeta de entrada en esta instancia de Azure Blob Storage. 
> - Especifique un nombre globalmente único para el generador de datos para el parámetro **dataFactoryName**. Por ejemplo: ARMTutorialFactoryJohnDoe11282017. 
> - En **triggerStartTime**, especifique el día actual con el formato: `2017-11-28T00:00:00`.
> - En **triggerEndTime**, especifique el día siguiente con el formato: `2017-11-29T00:00:00`. También puede comprobar la hora UTC actual y especificar la hora, o dos horas, siguiente como hora de finalización. Por ejemplo, si la hora UTC actual es 1:32 a. m., especifique `2017-11-29:03:00:00` como la hora de finalización. En ese caso, el desencadenador ejecuta la canalización dos veces (a las 2 a. m. y a las 3 a. m.).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataFactoryName": {
      "value": "<datafactoryname>"
    },    
    "dataFactoryLocation": {
      "value": "East US"
    },
    "storageAccountName": {
      "value": "<yourstroageaccountname>"
    },
    "storageAccountKey": {
      "value": "<yourstorageaccountkey>"
    },
    "blobContainer": {
      "value": "adftutorial"
    },
    "inputBlobFolder": {
      "value": "input"
    },
    "inputBlobName": {
      "value": "emp.txt"
    },
    "outputBlobFolder": {
      "value": "output"
    },
    "outputBlobName": {
      "value": "emp.txt"
    },
    "triggerStartTime": {
        "value": "2017-11-28T00:00:00. Set to today"
    },
    "triggerEndTime": {
        "value": "2017-11-29T00:00:00. Set to tomorrow"
    }
  }
}
```

> [!IMPORTANT]
> Puede tener archivos JSON de parámetros independientes para los entornos de desarrollo, pruebas y producción, que puede usar con la misma plantilla JSON de Data Factory. Al usar un script de PowerShell, puede automatizar la implementación de las entidades de Data Factory en estos entornos. 

## <a name="deploy-data-factory-entities"></a>Implementación de entidades de Data Factory 
En PowerShell, ejecute el siguiente comando para implementar las entidades de Data Factory mediante la plantilla de Resource Manager que ha creado en esta guía de inicio rápido. 

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFTutorial\ADFTutorialARM.json -TemplateParameterFile C:\ADFTutorial\ADFTutorialARM-Parameters.json
```

El resultado se parecerá al del ejemplo siguiente: 

```
DeploymentName          : MyARMDeployment
ResourceGroupName       : ADFTutorialResourceGroup
ProvisioningState       : Succeeded
Timestamp               : 11/29/2017 3:11:13 AM
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                 Type            Value
                          ===============      ============    ==========
                          dataFactoryName      String          <data factory name>
                          dataFactoryLocation  String          East US
                          storageAccountName   String          <storage account name>
                          storageAccountKey    SecureString
                          blobContainer        String          adftutorial
                          inputBlobFolder      String          input
                          inputBlobName        String          emp.txt
                          outputBlobFolder     String          output
                          outputBlobName       String          emp.txt
                          triggerStartTime     String          11/29/2017 12:00:00 AM
                          triggerEndTime       String          11/29/2017 4:00:00 AM

Outputs                 :
DeploymentDebugLogLevel :
```

## <a name="start-the-trigger"></a>Inicio del desencadenador

La plantilla implementa las siguientes entidades de Data Factory: 

- Servicio vinculado de Azure Storage
- Conjuntos de datos del blob de Azure (entrada y salida)
- Canalización con actividad de copia
- Desencadenador para desencadenar la canalización

El desencadenador implementado está en estado detenido. Una de las formas de iniciar el desencadenador consiste en usar el cmdlet de PowerShell **Start-AzureRmDataFactoryV2Trigger**. El procedimiento siguiente proporciona los pasos detallados: 

1. En la ventana de PowerShell, cree una variable que contenga el nombre del grupo de recursos. Copie el siguiente comando en la ventana de PowerShell y presione ENTRAR. Si ha especificado otro nombre de grupo de recursos en el comando New-AzureRmResourceGroupDeployment, actualice el valor aquí. 

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ``` 
1. Cree una variable que contenga el nombre de la factoría de datos. Especifique el mismo nombre que especificó en el archivo ADFTutorialARM Parameters.json.   

    ```powershell
    $dataFactoryName = "<yourdatafactoryname>"
    ```
3. Establezca una variable para el nombre del desencadenador. El nombre del desencadenador está codificado de forma rígida en el archivo de plantilla de Resource Manager (ADFTutorialARM.json).

    ```powershell
    $triggerName = "ArmTemplateTestTrigger"
    ```
4. Obtenga el **estado del desencadenador**, para lo que debe ejecutar el siguiente comando de PowerShell después de especificar el nombre de la factoría de datos y del desencadenador:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

    Este es la salida de ejemplo: 

    ```json
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ARMFactory1128
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Stopped
    ```
    
    Tenga en cuenta que el estado del runtime del desencadenador es **Detenido**. 
5. **Inicio del desencadenador**. El desencadenador ejecuta la canalización que se define en la plantilla a la hora. Es decir, si este comando se ejecutó a las 2:25 p. m., el desencadenador ejecuta la canalización la primera vez a las 3 p. m. Luego, ejecuta la canalización cada hora hasta la hora de finalización que se especificó para el desencadenador. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Este es la salida de ejemplo: 
    
    ```
    Confirm
    Are you sure you want to start trigger 'ArmTemplateTestTrigger' in data factory 'ARMFactory1128'?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
    True
    ```
6. Confirme que el desencadenador se ha iniciado, para lo que debe volver a ejecutar el comando Get-AzureRmDataFactoryV2Trigger.  

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -TriggerName $triggerName
    ```
    
    Este es la salida de ejemplo:
    
    ```
    TriggerName       : ArmTemplateTestTrigger
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ARMFactory1128
    Properties        : Microsoft.Azure.Management.DataFactory.Models.ScheduleTrigger
    RuntimeState      : Started
    ```

## <a name="monitor-the-pipeline"></a>Supervisar la canalización
1. Después de iniciar sesión en [Azure Portal](https://portal.azure.com/), haga clic en **Más servicios**, busque la palabra clave, como **data fa** y seleccione **Factorías de datos**.

    ![Examinar el menú de Factorías de datos](media/quickstart-create-data-factory-resource-manager-template/browse-data-factories-menu.png)
2. En la página **Factorías de datos**, haga clic en la factoría de datos que ha creado. Si es necesario, filtre la lista por el nombre de su factoría de datos.  

    ![Selección de la factoría de datos](media/quickstart-create-data-factory-resource-manager-template/select-data-factory.png)
3. En la página Factoría de datos, haga clic en el icono **Supervisión y administración**. 

    ![Icono Supervisión y administración](media/quickstart-create-data-factory-resource-manager-template/monitor-manage-tile.png)
4. La **Aplicación de integración de datos** se debe abrir en una pestaña independiente del explorador web. Si la pestaña Supervisar no está activa, cambie a la **pestaña Supervisar**. Tenga en cuenta que la ejecución de la canalización la desencadenó un **desencadenador del programador**. 

    ![Supervisar ejecución de canalización](media/quickstart-create-data-factory-resource-manager-template/monitor-pipeline-run.png)    

    > [!IMPORTANT]
    > Verá que la canalización se ejecuta solo en a las horas en punto (por ejemplo: 4 a. m., 5 a. m., 6 a. m., etc.) Haga clic en **Actualizar** en la barra de herramientas para actualizar la lista cuando la hora que alcance la hora siguiente. 
5. Haga clic en el vínculo de las columnas **Acciones**. 

    ![Vínculo de acciones de canalización](media/quickstart-create-data-factory-resource-manager-template/pipeline-actions-link.png)
6. Vea las ejecuciones de actividad asociadas con la ejecución de la canalización. En esta guía de inicio rápido, la canalización tiene sólo una actividad del tipo: Copy. Por consiguiente, se ve una ejecución de dicha actividad. 

    ![Ejecuciones de actividad](media/quickstart-create-data-factory-resource-manager-template/activity-runs.png)
1. Haga clic en el vínculo situado debajo de la columna **Output**. Se ve el resultado de la operación de copia en la ventana de **salida**. Haga clic en el botón de maximizar para ver la salida completa. Puede cerrar la ventana de salida maximizada. 

    ![Ventana de salida](media/quickstart-create-data-factory-resource-manager-template/output-window.png)
7. Una vez que vea que la ejecución se ha realizado correctamente o se ha producido errores, detenga el desencadenador. El desencadenador ejecuta la canalización cada hora. La canalización copia el mismo archivo de la carpeta de entrada a la carpeta de salida en cada ejecución. Para detener el desencadenador, escriba el siguiente comando en la ventana de PowerShell. 
    
    ```powershell
    Stop-AzureRmDataFactoryV2Trigger -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $triggerName
    ```

[!INCLUDE [data-factory-quickstart-verify-output-cleanup.md](../../includes/data-factory-quickstart-verify-output-cleanup.md)] 

## <a name="json-definitions-for-entities"></a>Definiciones de JSON para entidades
Las siguientes entidades de Data Factory se definen en la plantilla JSON: 

- [Servicio vinculado de Azure Storage](#azure-storage-linked-service)
- [Conjunto de datos de entrada de blob de Azure:](#azure-blob-input-dataset)
- [Conjunto de datos de salida de blob de Azure](#azure-blob-output-dataset)
- [Canalización de datos con una actividad de copia](#data-pipeline)
- [Desencadenador](#trigger)

#### <a name="azure-storage-linked-service"></a>Servicio vinculado de Azure Storage
AzureStorageLinkedService vincula una cuenta de Azure Storage a la factoría de datos. Como parte de los requisitos previos, ha creado un contenedor y ha cargado datos en esta cuenta de almacenamiento. Especifique el nombre y la clave de la cuenta de almacenamiento de Azure en esta sección. Consulte [Servicio vinculado de Azure Storage](connector-azure-blob-storage.md#linked-service-properties) para más información sobre las propiedades JSON usadas para definir un servicio vinculado de Azure Storage. 

```json
{
    "type": "linkedservices",
    "name": "[variables('azureStorageLinkedServiceName')]",
    "dependsOn": [
        "[parameters('dataFactoryName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
        "type": "AzureStorage",
        "description": "Azure Storage linked service",
        "typeProperties": {
            "connectionString": {
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]",
                "type": "SecureString"
            }
        }
    }
}
```

ConnectionString utiliza los parámetros storageAccountName y storageAccountKey. Los valores de estos parámetros se pasan mediante el uso de un archivo de configuración. La definición también usa las variables azureStroageLinkedService y dataFactoryName definidas en la plantilla. 

#### <a name="azure-blob-input-dataset"></a>Conjunto de datos de entrada de blob de Azure
El servicio vinculado Azure Storage especifica la cadena de conexión que el servicio Data Factory utiliza en tiempo de ejecución para conectarse a su cuenta de Azure Storage. En la definición del conjunto de datos de blob de Azure, especifique los nombres del contenedor de blobs, la carpeta y el archivo que contiene los datos de entrada. Consulte las [propiedades del conjunto de datos de Azure Blob](connector-azure-blob-storage.md#dataset-properties) para más información sobre las propiedades JSON usadas para definir un conjunto de datos de Azure Blob. 

```json
{
    "type": "datasets",
    "name": "[variables('inputDatasetName')]",
    "dependsOn": [
    "[parameters('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'), '/')]",
        "fileName": "[parameters('inputBlobName')]"
    },
    "linkedServiceName": {
        "referenceName": "[variables('azureStorageLinkedServiceName')]",
        "type": "LinkedServiceReference"
    }
    }
},

```

#### <a name="azure-blob-output-dataset"></a>Conjunto de datos de salida del blob de Azure
Especifique el nombre de la carpeta en la instancia de Azure Blob Storage que contiene los datos copiados desde la carpeta de entrada. Consulte las [propiedades del conjunto de datos de Azure Blob](connector-azure-blob-storage.md#dataset-properties) para más información sobre las propiedades JSON usadas para definir un conjunto de datos de Azure Blob. 

```json
{
    "type": "datasets",
    "name": "[variables('outputDatasetName')]",
    "dependsOn": [
    "[parameters('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'), '/')]",
        "fileName": "[parameters('outputBlobName')]"
    },
    "linkedServiceName": {
        "referenceName": "[variables('azureStorageLinkedServiceName')]",
        "type": "LinkedServiceReference"
    }
    }
}
```

#### <a name="data-pipeline"></a>Canalización de datos
Defina una canalización que copie los datos de un conjunto de datos del blob de Azure a otro. Consulte [JSON de canalización](concepts-pipelines-activities.md#pipeline-json) para obtener descripciones de elementos JSON que se usan para definir una canalización en este ejemplo. 

```json
{
    "type": "pipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
    "[parameters('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]",
    "[variables('inputDatasetName')]",
    "[variables('outputDatasetName')]"
    ],
    "apiVersion": "2017-09-01-preview",
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
        "name": "MyCopyActivity",
        "inputs": [
            {
            "referenceName": "[variables('inputDatasetName')]",
            "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
            "referenceName": "[variables('outputDatasetName')]",
            "type": "DatasetReference"
            }
        ]
        }
    ]
    }
}
```

#### <a name="trigger"></a>Desencadenador
Defina un desencadenador que ejecute la canalización cada hora. El desencadenador implementado está en estado detenido. Inicie el desencadenador mediante el cmdlet **Start-AzureRmDataFactoryV2Trigger**. Para más información acerca de los desencadenadores, consulte el artículo [Ejecución y desencadenadores de canalización en Azure Data Factory](concepts-pipeline-execution-triggers.md#triggers). 

```json
{
    "type": "triggers",
    "name": "[variables('triggerName')]",
    "dependsOn": [
        "[parameters('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('inputDatasetName')]",
        "[variables('outputDatasetName')]",
        "[variables('pipelineName')]"
    ],
    "apiVersion": "2017-09-01-preview",
    "properties": {
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-28T00:00:00",
                "endTime": "2017-11-29T00:00:00",
                "timeZone": "UTC"               
            }
        },
        "pipelines": [{
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "ArmtemplateSampleCopyPipeline"
            },
            "parameters": {}
        }]
    }
}
```

## <a name="reuse-the-template"></a>Reutilización de la plantilla
En el tutorial, ha creado una plantilla para definir las entidades de Data Factory y una plantilla para pasar valores de parámetros. Para usar la misma plantilla para implementar las entidades de Data Factory en distintos entornos, cree un archivo de parámetros para cada entorno y utilícelo al implementarla en ese entorno.     

Ejemplo:  

```PowerShell
New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json
```
Tenga en cuenta que el primer comando usa el archivo de parámetros para el entorno de desarrollo, el segundo para el entorno de prueba y el tercero para el entorno de producción.  

También puede volver a usar la plantilla para llevar a cabo tareas repetidas. Por ejemplo, cree muchas factorías de datos con una o varias canalizaciones que implementen la misma lógica, pero cada factoría de datos usa cuentas de Azure Storage diferentes. En este escenario, usa la misma plantilla en el mismo entorno (desarrollo, prueba o producción) con distintos archivos de parámetros para crear factorías de datos. 


## <a name="next-steps"></a>pasos siguientes
La canalización de este ejemplo copia los datos de una ubicación a otra en una instancia de Azure Blob Storage. Consulte los [tutoriales](tutorial-copy-data-dot-net.md) para obtener información acerca del uso de Data Factory en otros escenarios. 
