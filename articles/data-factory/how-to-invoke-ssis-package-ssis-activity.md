---
title: Ejecución de un paquete de SSIS mediante una actividad de SSIS de Azure Data Factory | Microsoft Docs
description: En este artículo se describe cómo ejecutar un paquete de SQL Server Integration Services (SSIS) desde una canalización de Azure Data Factory mediante la actividad de SSIS.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: article
ms.date: 04/17/2018
ms.author: douglasl
ms.openlocfilehash: 6c8bbe7ef7f74638b978cdad5b59a89fd81d12a5
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="run-an-ssis-package-using-the-ssis-activity-in-azure-data-factory"></a>Ejecución de un paquete de SSIS mediante una actividad de SSIS de Azure Data Factory
En este artículo se describe cómo ejecutar un paquete de SSIS desde una canalización de Azure Data Factory mediante una actividad de SSIS. 

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. La actividad de SSIS no está disponible en la versión 1 del servicio Data Factory, que está disponible con carácter general. Si desea ver un método alternativo para ejecutar un paquete SSIS con la versión 1 del servicio de Data Factory, vea [Invocación de un paquete de SSIS mediante una actividad de procedimiento almacenado de Azure Data Factory](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>requisitos previos

### <a name="azure-sql-database"></a>Azure SQL Database 
El tutorial de este artículo usa una instancia de Azure SQL Database que hospeda el catálogo de SSIS. También puede usar una instancia administrada de Azure SQL (versión preliminar).

## <a name="create-an-azure-ssis-integration-runtime"></a>Creación de una instancia de Integration Runtime de SSIS de Azure
Cree una instancia de Integration Runtime de SSIS de Azure si no tiene ninguna. Para ello, siga las instrucciones paso a paso del [tutorial Implementación paquetes de SSIS en Azure](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Interfaz de usuario de Data Factory (Azure Portal)
En esta sección, usará la interfaz de usuario de Data Factory para crear una canalización de Data Factory con una actividad de SSIS que invoca un paquete SSIS.

### <a name="create-a-data-factory"></a>Crear una factoría de datos
El primer paso es crear una factoría de datos con Azure Portal. 

1. Inicie el explorador web **Microsoft Edge** o **Google Chrome**. Actualmente, la interfaz de usuario de Data Factory solo se admite en los exploradores web Microsoft Edge y Google Chrome.
2. Acceda a [Azure Portal](https://portal.azure.com). 
3. En el menú de la izquierda, haga clic en **Nuevo**, **Datos y análisis** y **Factoría de datos**. 
   
   ![New->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. En la página **New data factory** (Nueva factoría de datos), escriba **ADFTutorialDataFactory** en **Name** (Nombre). 
      
     ![Página New data factory (Nueva factoría de datos)](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   El nombre de Azure Data Factory debe ser **único de forma global**. Si ve el siguiente error en el campo del nombre, cambie el nombre de la factoría de datos (por ejemplo, yournameADFTutorialDataFactory). Consulte el artículo [Azure Data Factory: reglas de nomenclatura](naming-rules.md) para conocer las reglas de nomenclatura de los artefactos de Data Factory.
  
     ![Error de nombre no disponible](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Seleccione la **suscripción** de Azure donde desea crear la factoría de datos. 
4. Para el **grupo de recursos**, realice uno de los siguientes pasos:
     
      - Seleccione en primer lugar **Usar existente**y después un grupo de recursos de la lista desplegable. 
      - Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos.   
         
    Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/resource-group-overview.md).  
4. Seleccione **V2 (versión preliminar)** como **versión**.
5. Seleccione la **ubicación** de Data Factory. En la lista desplegable solo se muestran las ubicaciones que admite Data Factory. Los almacenes de datos (Azure Storage, Azure SQL Database, etc.) y los procesos (HDInsight, etc.) utilizados por la factoría de datos pueden encontrarse en otras ubicaciones.
6. Seleccione **Anclar al panel**.     
7. Haga clic en **Create**(Crear).
8. En el panel, verá el icono siguiente con el estado: **Implementando factoría de datos**. 

    ![icono implementando factoría de datos](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Una vez completada la creación, verá la página **Data Factory** tal como se muestra en la imagen.
   
    ![Página principal Factoría de datos](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Haga clic en el icono **Author & Monitor** (Creación y supervisión) para iniciar la aplicación de interfaz de usuario de Azure Data Factory en una pestaña independiente. 

### <a name="create-a-pipeline-with-an-ssis-activity"></a>Creación de una canalización con una actividad de SSIS
En este paso, usa la interfaz de Data Factory para crear una canalización. Agregue una actividad de SSIS a la canalización y configúrela para ejecutar el paquete de SSIS. 

1. En la página de introducción, haga clic en **Create pipeline** (Crear canalización): 

    ![Página de introducción](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. En el cuadro de herramientas **Actividades**, expanda **General**, arrastre la actividad **Ejecutar paquete de SSIS** y colóquela en la superficie del diseñador de canalizaciones. 

   ![Acción de arrastre de la actividad de SSIS a la superficie del diseñador](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. En la pestaña **General** de las propiedades de la actividad de SSIS, proporcione un nombre y una descripción para la actividad. Establezca el tiempo de espera opcional y los valores de reintento.

    ![Establecimiento de propiedades en la pestaña General](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. En la pestaña **Configuración** de las propiedades de la actividad de SSIS, seleccione el entorno de ejecución de integración de Azure SSIS asociado con la base de datos`SSISDB` donde se implementó el paquete. Proporcione la ruta de acceso del paquete en la base de datos `SSISDB` en el formato `<folder name>/<project name>/<package name>.dtsx`. Si lo desea, especifique la ejecución de 32 bits y un nivel de registro predefinido o personalizado, y proporcione una ruta de acceso de entorno en el formato `<folder name>/<environment name>`.

    ![Establecimiento de las propiedades en la pestaña Configuración](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

5. Para validar la configuración de la canalización, haga clic en **Validate** (Validar) en la barra de herramientas. Para cerrar **Pipeline Validation Report** (Informe de comprobación de la canalización), haga clic en **>>**.

6. Publique la canalización en Data Factory con un clic en el botón **Publish All** (Publicar todo). 

### <a name="run-and-monitor-the-pipeline"></a>Ejecución y supervisión de la canalización
En esta sección, desencadena una ejecución de canalización y luego la supervisa. 

1. Para desencadenar una ejecución de canalización, haga clic en **Trigger** (Desencadenar) en la barra de herramientas y en **Trigger now** (Desencadenar ahora). 

    ![Trigger now (Desencadenar ahora)](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. En la ventana **Pipeline Run** (Ejecución de canalización), seleccione **Finish** (Finalizar). 
3. Cambie a la pestaña **Monitor** (Supervisar) de la izquierda. Verá la ejecución de canalización y su estado junto con otro tipo de información (como la hora de inicio de la ejecución). Para actualizar la vista, haga clic en **Refresh** (Actualizar).

    ![Ejecuciones de la canalización](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Haga clic en el vínculo **View Activity Runs** (Ver ejecuciones de actividad) de la columna **Actions** (Acciones). Solo verá una ejecución de actividad porque la canalización solo tiene una actividad (actividad de SSIS).

    ![Ejecuciones de actividad](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

4. Puede ejecutar la **consulta** siguiente en la base de datos SSISDB en el servidor de Azure SQL para comprobar la ejecución del paquete. 

    ```sql
    select * from catalog.executions
    ```

    ![Comprobación de las ejecuciones del paquete](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> También puede crear un desencadenador programado para la canalización de manera que esta se ejecute según una programación (por hora, cada día, etc.). Para ver un ejemplo, consulte [Create a data factory - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule) (Creación de una factoría de datos: interfaz de usuario de Data Factory).

## <a name="azure-powershell"></a>Azure PowerShell
En esta sección, usará Azure PowerShell para crear una canalización de Data Factory con una actividad de procedimiento almacenado que invoca un paquete de SSIS. 

Instale los módulos de Azure PowerShell siguiendo las instrucciones de [Cómo instalar y configurar Azure PowerShell](/powershell/azure/install-azurerm-ps). 

### <a name="create-a-data-factory"></a>Crear una factoría de datos
Puede usar la misma factoría de datos que tiene el IR de SSIS de Azure o crear una factoría de datos independiente. El siguiente procedimiento detalla los pasos para crear una factoría de datos. Creará una canalización con una actividad de SSIS en la factoría de datos. La actividad de SSIS ejecuta el paquete de SSIS. 

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
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
                                            -Location $ResGrp.Location `
                                            -Name $dataFactoryName 
    ```

Tenga en cuenta los siguientes puntos:

* El nombre del generador de datos de Azure debe ser único global. Si recibe el siguiente error, cambie el nombre y vuelva a intentarlo.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Para crear instancias de Data Factory, la cuenta de usuario que use para iniciar sesión en Azure debe ser un miembro de los roles **colaborador** o **propietario**, o de **administrador** de la suscripción de Azure.
* Actualmente, la versión 2 de Data Factory permite crear factorías de datos solo en las regiones Este de EE. UU., Este de EE. UU. 2, Europa Occidental y Asia Suroriental. Los almacenes de datos (Azure Storage, Azure SQL Database, etc.) y los procesos (HDInsight, etc.) que usa la factoría de datos pueden encontrarse en otras regiones.

### <a name="create-a-pipeline-with-an-ssis-activity"></a>Creación de una canalización con una actividad de SSIS 
En este paso, se crea una canalización con una actividad de SSIS. La actividad ejecuta el paquete de SSIS. 

1. Cree un archivo JSON con el nombre **RunSSISPackagePipeline.json** en la carpeta **C:\ADF\RunSSISPackage** con un contenido similar al del siguiente ejemplo:

    > [!IMPORTANT]
    > Reemplace los nombres de objeto, descripciones, rutas de acceso, valores de propiedades y parámetros, contraseñas y otros valores de variables antes de guardar el archivo. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [{
                "name": "mySSISActivity",
                "description": "My SSIS package/activity description",
                "type": "ExecuteSSISPackage",
                "typeProperties": {
                    "connectVia": {
                        "referenceName": "myAzureSSISIR",
                        "type": "IntegrationRuntimeReference"
                    },
                    "runtime": "x64",
                    "loggingLevel": "Basic",
                    "packageLocation": {
                        "packagePath": "FolderName/ProjectName/PackageName.dtsx"            
                    },
                    "environmentPath":   "FolderName/EnvironmentName",
                    "projectParameters": {
                        "project_param_1": {
                            "value": "123"
                        }
                    },
                    "packageParameters": {
                        "package_param_1": {
                            "value": "345"
                        }
                    },
                    "projectConnectionManagers": {
                        "MyAdonetCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "abc"
                                }
                            }
                        }
                    },
                    "packageConnectionManagers": {
                        "MyOledbCM": {
                            "userName": {
                                "value": "sa"
                            },
                            "passWord": {
                                "value": {
                                    "type": "SecureString",
                                    "value": "def"
                                }
                            }
                        }
                    },
                    "propertyOverrides": {
                        "\\PackageName.dtsx\\MaxConcurrentExecutables ": {
                            "value": 8,
                            "isSensitive": false
                        }
                    }
                },
                "policy": {
                    "timeout": "0.01:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30
                }
            }]
        }
    }
    ```

2.  En Azure PowerShell, cambie a la carpeta `C:\ADF\RunSSISPackage`.

3. Para crear la canalización **RunSSISPackagePipeline**, ejecute el cmdlet **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                   -ResourceGroupName $ResGrp.ResourceGroupName `
                                                   -Name "RunSSISPackagePipeline"
                                                   -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Este es la salida de ejemplo:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Creación de una ejecución de canalización
Use el cmdlet **Invoke-AzureRmDataFactoryV2Pipeline** para ejecutar la canalización. El cmdlet devuelve el identificador de ejecución de la canalización para realizar una supervisión en un futuro.

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>Supervisión de la ejecución de la canalización

Ejecute el script de PowerShell siguiente para comprobar continuamente el estado de ejecución de la canalización hasta que termine de copiar los datos. Copie y pegue el siguiente script en la ventana de PowerShell y presione ENTRAR. 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

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

### <a name="create-a-trigger"></a>Crear un desencadenador
En el paso anterior, ejecutó la canalización a petición. También puede crear un desencadenador de programación para ejecutar la canalización en una programación (cada hora, día, etc.).

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
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                    -DataFactoryName $DataFactory.DataFactoryName `
                                    -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. De manera predeterminada, el desencadenador está en estado detenido. Para iniciar el desencadenador ejecute el cmdlet **Start-AzureRmDataFactoryV2Trigger**. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                      -DataFactoryName $DataFactory.DataFactoryName `
                                      -Name "MyTrigger" 
    ```
5. Confirme que el desencadenador se haya iniciado con la ejecución del cmdlet **Get-AzureRmDataFactoryV2Trigger**. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                    -DataFactoryName $DataFactoryName `
                                    -Name "MyTrigger"     
    ```    
6. Ejecute el comando siguiente al comenzar la hora siguiente. Por ejemplo, si la hora actual es 15:25 UTC, ejecute el comando a las 16:00 UTC. 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                       -DataFactoryName $DataFactoryName `
                                       -TriggerName "MyTrigger" `
                                       -TriggerRunStartedAfter "2017-12-06" `
                                       -TriggerRunStartedBefore "2017-12-09"
    ```

    Puede ejecutar la consulta siguiente en la base de datos SSISDB en el servidor de Azure SQL para comprobar la ejecución del paquete. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Pasos siguientes
También puede supervisar la canalización mediante Azure Portal. Para ver instrucciones paso a paso, consulte [Supervisar la canalización](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
