---
title: "Invocación del paquete de SSIS mediante Azure Data Factory: actividad de procedimiento almacenado | Microsoft Docs"
description: "En este artículo se describe cómo invocar un paquete de SQL Server Integration Services (SSIS) desde una canalización de Azure Data Factory mediante la actividad de procedimiento almacenado."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: article
ms.date: 12/07/2017
ms.author: jingwang
ms.openlocfilehash: 713e9ad7a76c15cbde912954e00991a80b995683
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Invocación de un paquete de SSIS mediante una actividad de procedimiento almacenado de Azure Data Factory
En este artículo se describe cómo invocar un paquete de SSIS desde una canalización de Azure Data Factory mediante una actividad de procedimiento almacenado. 

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte el tema sobre la [invocación de paquetes de SSIS mediante una actividad de procedimiento almacenado en la versión 1](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>requisitos previos

### <a name="azure-sql-database"></a>Azure SQL Database 
El tutorial de este artículo usa una instancia de Azure SQL Database que hospeda el catálogo de SSIS. También puede usar una instancia administrada de Azure SQL (versión preliminar privada).

## <a name="create-an-azure-ssis-integration-runtime"></a>Creación de una instancia de Integration Runtime de SSIS de Azure
Cree una instancia de Integration Runtime de SSIS de Azure si no tiene ninguna. Para ello, siga las instrucciones paso a paso del [tutorial Implementación paquetes de SSIS en Azure](tutorial-deploy-ssis-packages-azure.md).

### <a name="azure-powershell"></a>Azure PowerShell
Instale los módulos de Azure PowerShell siguiendo las instrucciones de [Cómo instalar y configurar Azure PowerShell](/powershell/azure/install-azurerm-ps). 

## <a name="create-a-data-factory"></a>Crear una factoría de datos
Puede usar la misma factoría de datos que tiene el IR de SSIS de Azure o crear una factoría de datos independiente. El siguiente procedimiento detalla los pasos para crear una factoría de datos. Debe crear una canalización con una actividad de procedimiento almacenado en esta factoría de datos. La actividad de procedimiento almacenado ejecuta un procedimiento almacenado en la base de datos SSISDB para ejecutar el paquete de SSIS. 

1. Defina una variable para el nombre del grupo de recursos que usa en los comandos de PowerShell más adelante. Copie el texto del comando siguiente en PowerShell, especifique el nombre del [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) entre comillas dobles y ejecute el comando. Por ejemplo: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Si el grupo de recursos ya existe, puede que no desee sobrescribirlo. Asigne otro valor a la variable `$ResourceGroupName` y vuelva a ejecutar el comando
2. Para crear el grupo de recursos de Azure, ejecute el comando siguiente: 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Si el grupo de recursos ya existe, puede que no desee sobrescribirlo. Asigne otro valor a la variable `$ResourceGroupName` y ejecute el comando de nuevo. 
3. Defina una variable para el nombre de la factoría de datos. 

    > [!IMPORTANT]
    >  Actualice el nombre de la factoría de datos para que sea globalmente único. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Para crear la factoría de datos, ejecute el siguiente cmdlet **Set-AzureRmDataFactoryV2** con las propiedades ResourceGroupName y Location de la variable $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Tenga en cuenta los siguientes puntos:

* El nombre de la factoría de datos de Azure debe ser único global. Si recibe el siguiente error, cambie el nombre y vuelva a intentarlo.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Para crear instancias de Data Factory, la cuenta de usuario que use para iniciar sesión en Azure debe ser un miembro de los roles **colaborador** o **propietario**, o de **administrador** de la suscripción de Azure.
* Actualmente, la versión 2 de Data Factory permite crear factorías de datos solo en las regiones Este de EE. UU., Este de EE. UU. 2 y Europa Occidental. Los almacenes de datos (Azure Storage, Azure SQL Database, etc.) y los procesos (HDInsight, etc.) que usa la factoría de datos pueden encontrarse en otras regiones.

### <a name="create-an-azure-sql-database-linked-service"></a>Creación de un servicio vinculado a Azure SQL Database
Cree un servicio vinculado para vincular su instancia de Azure SQL Database que hospeda el catálogo de SSIS con la factoría de datos. Data Factory usa la información de este servicio vinculado para conectarse a la base de datos SSISDB y ejecuta un procedimiento almacenado para ejecutar un paquete de SSIS. 

1. Cree un archivo JSON denominado **AzureSQLDatabaseLinkedService.json** en la carpeta **C:\ADF\RunSSISPackage** con el siguiente contenido: 

    > [!IMPORTANT]
    > Reemplace &lt;servername&gt;, &lt;username&gt; y &lt;password&gt; por los nombres de su instancia de Azure SQL Database antes de guardar el archivo.

    ```json
    {
        "name": "AzureSqlDbLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. En **Azure PowerShell**, cambie a la carpeta **C:\ADF\RunSSISPackage**.

3. Ejecute el cmdlet **Set-AzureRmDataFactoryV2LinkedService** para crear el servicio vinculado: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

## <a name="create-a-pipeline-with-stored-procedure-activity"></a>Crear una canalización con una actividad de procedimiento almacenado 
En este paso, debe crear una canalización con una actividad de procedimiento almacenado. La actividad invoca el procedimiento almacenado sp_executesql para ejecutar su paquete de SSIS. 

1. Cree un archivo JSON con el nombre **RunSSISPackagePipeline.json** en la carpeta **C:\ADF\RunSSISPackage** con el siguiente contenido:

    > [!IMPORTANT]
    > Reemplace &lt;FOLDER NAME&gt;, &lt;PROJECT NAME&gt; y &lt;PACKAGE NAME&gt; por los nombres de carpeta, proyecto y paquete del catálogo de SSIS antes de guardar el archivo. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDbLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. Para crear la canalización **RunSSISPackagePipeline**, ejecute el cmdlet **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
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
Use el cmdlet **Invoke-AzureRmDataFactoryV2Pipeline** para ejecutar la canalización. El cmdlet devuelve el identificador de ejecución de la canalización para realizar una supervisión en un futuro.

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

## <a name="monitor-the-pipeline-run"></a>Supervisión de la ejecución de la canalización

Ejecute el script de PowerShell siguiente para comprobar continuamente el estado de ejecución de la canalización hasta que termine de copiar los datos. Copie y pegue el siguiente script en la ventana de PowerShell y presione ENTRAR. 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

## <a name="create-a-trigger"></a>Crear un desencadenador
En el paso anterior, invocó la canalización a petición. También puede crear un desencadenador de programación para ejecutar la canalización en una programación (cada hora, día, etc.).

1. Cree un archivo JSON con el nombre **MyTrigger.json** en la carpeta **C:\ADF\RunSSISPackage** con el siguiente contenido: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. En **Azure PowerShell**, cambie a la carpeta **C:\ADF\RunSSISPackage**.
3. Ejecute el cmdlet **Set-AzureRmDataFactoryV2Trigger** para crear el desencadenador. 

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. De manera predeterminada, el desencadenador está en estado detenido. Para iniciar el desencadenador ejecute el cmdlet **Start-AzureRmDataFactoryV2Trigger**. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Confirme que el desencadenador se haya iniciado con la ejecución del cmdlet **Get-AzureRmDataFactoryV2Trigger**. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Ejecute el comando siguiente al comenzar la hora siguiente. Por ejemplo, si la hora actual es 15:25 UTC, ejecute el comando a las 16:00 UTC. 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    Puede ejecutar la consulta siguiente en la base de datos SSISDB en el servidor de Azure SQL para comprobar la ejecución del paquete. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>pasos siguientes
También puede supervisar la canalización mediante Azure Portal. Para ver instrucciones paso a paso, consulte [Supervisar la canalización](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
