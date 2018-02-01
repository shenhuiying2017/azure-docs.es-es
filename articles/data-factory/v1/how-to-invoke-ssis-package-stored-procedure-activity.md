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
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 66b4f068189fd17f08a6a57ed44233c04c16fff7
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/20/2018
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Invocación de un paquete de SSIS mediante una actividad de procedimiento almacenado de Azure Data Factory
En este artículo se describe cómo invocar un paquete de SSIS desde una canalización de Azure Data Factory mediante una actividad de procedimiento almacenado. 

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory, que está disponible con carácter general. Si usa la versión 2 del servicio Data Factory, disponible con la versión preliminar pública, consulte el tema sobre la [invocación de paquetes de SSIS mediante una actividad de procedimiento almacenado en la versión 2](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>requisitos previos

### <a name="azure-sql-database"></a>Azure SQL Database 
El tutorial de este artículo usa una instancia de Azure SQL Database que hospeda el catálogo de SSIS. También puede usar una instancia administrada de Azure SQL (versión preliminar privada).

### <a name="create-an-azure-ssis-integration-runtime"></a>Creación de una instancia de Integration Runtime de SSIS de Azure
Cree una instancia de Integration Runtime de SSIS de Azure si no tiene ninguna. Para ello, siga las instrucciones paso a paso del [tutorial Implementación paquetes de SSIS en Azure](../tutorial-deploy-ssis-packages-azure.md). Debe crear una factoría de datos de la versión 2 para crear una instancia de Integration Runtime de SSIS de Azure. 

## <a name="azure-portal"></a>Azure Portal
En esta sección, se usa Azure Portal para crear una canalización de Data Factory con una actividad de procedimiento almacenado que invoca un paquete SSIS.

### <a name="create-a-data-factory"></a>Crear una factoría de datos
El primer paso es crear una factoría de datos con Azure Portal. 

1. Acceda a [Azure Portal](https://portal.azure.com). 
2. En el menú de la izquierda, haga clic en **Nuevo**, **Datos y análisis** y **Factoría de datos**. 
   
   ![New->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. En la página **New data factory** (Nueva factoría de datos), escriba **ADFTutorialDataFactory** en **Name** (Nombre). 
      
     ![Página New data factory (Nueva factoría de datos)](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   El nombre de Azure Data Factory debe ser **único de forma global**. Si ve el siguiente error en el campo del nombre, cambie el nombre de la factoría de datos (por ejemplo, yournameADFTutorialDataFactory). Consulte el artículo [Azure Data Factory: reglas de nomenclatura](data-factory-naming-rules.md) para conocer las reglas de nomenclatura de los artefactos de Data Factory.

    `Data factory name ADFTutorialDataFactory is not available`
3. Seleccione la **suscripción** de Azure donde desea crear la factoría de datos. 
4. Para el **grupo de recursos**, realice uno de los siguientes pasos:
     
      - Seleccione en primer lugar **Usar existente**y después un grupo de recursos de la lista desplegable. 
      - Seleccione **Crear nuevo**y escriba el nombre de un grupo de recursos.   
         
    Para obtener más información sobre los grupos de recursos, consulte [Uso de grupos de recursos para administrar los recursos de Azure](../../azure-resource-manager/resource-group-overview.md).  
4. Seleccione **V1** para la **versión**.
5. Seleccione la **ubicación** de Data Factory. En la lista desplegable solo se muestran las ubicaciones que admite Data Factory. Los almacenes de datos (Azure Storage, Azure SQL Database, etc.) y los procesos (HDInsight, etc.) utilizados por la factoría de datos pueden encontrarse en otras ubicaciones.
6. Seleccione **Anclar al panel**.     
7. Haga clic en **Create**(Crear).
8. En el panel, verá el icono siguiente con el estado: **Implementando factoría de datos**. 

    ![icono implementando factoría de datos](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Una vez completada la creación, verá la página **Data Factory** tal como se muestra en la imagen.
   
    ![Página principal Factoría de datos](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Haga clic en **Crear e implementar** para iniciar Data Factory Editor.

    ![Editor de la Factoría de datos](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-editor.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Creación de un servicio vinculado a Azure SQL Database
Cree un servicio vinculado para vincular su instancia de Azure SQL Database que hospeda el catálogo de SSIS con la factoría de datos. Data Factory usa la información de este servicio vinculado para conectarse a la base de datos SSISDB y ejecuta un procedimiento almacenado para ejecutar un paquete de SSIS. 

1. En Data Factory Editor, haga clic en **Nuevo almacén de datos** en el menú y haga clic en **Azure SQL Database**. 

    ![Nuevo almacén de datos -> Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-sql-database-linked-service-menu.png)
2. En el panel de la derecha, realice los pasos siguientes:

    1. Reemplace `<servername>` por el nombre del servidor Azure SQL Server. 
    2. Reemplace `<databasename>` por **SSISDB** (nombre de la base de datos del catálogo SSIS). 
    3. Reemplace `<username@servername>` por el nombre del usuario que tiene acceso a Azure SQL Server. 
    4. Reemplace `<password>` por la contraseña del usuario. 
    5. Implemente el servicio vinculado haciendo clic en el botón **Implementar** de la barra de herramientas. 

        ![Servicio vinculado a Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-definition.png)

### <a name="create-a-dummy-dataset-for-output"></a>Crear un conjunto de datos ficticio de salida
Este conjunto de datos de salida es un conjunto de datos ficticio que controla la programación de la canalización. Observe que el valor frequency está establecido en Hour y que interval está establecido en 1. Por lo tanto, la canalización se ejecuta una vez por hora entre las horas de inicio y fin de la canalización. 

1. En el panel de la izquierda de Data Factory Editor, haga clic en **... Más** -> **Nuevo conjunto de datos** -> **Azure SQL**.

    ![Más -> Nuevo conjunto de datos](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-dataset-menu.png)
2. Copie el siguiente fragmento de código JSON en el editor de JSON del panel derecho. 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Haga clic en **Implementar** en la barra de herramientas. Esta acción implementa el conjunto de datos en el servicio Azure Data Factory. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Crear una canalización con una actividad de procedimiento almacenado 
En este paso, debe crear una canalización con una actividad de procedimiento almacenado. La actividad invoca el procedimiento almacenado sp_executesql para ejecutar su paquete de SSIS. 

1. En el panel izquierdo, haga clic en **... Más** y, luego, en **Nueva canalización**.
2. Copie el siguiente fragmento de código JSON en el editor de JSON: 

    > [!IMPORTANT]
    > Reemplace &lt;folder name&gt;, &lt;project name&gt; y &lt;package name&gt; por los nombres de carpeta, proyecto y paquete del catálogo de SSIS antes de guardar el archivo.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2018-01-19T00:00:00Z",
            "end": "2018-01-19T05:00:00Z",
            "isPaused": false
        }
    }    
    ```
3. Haga clic en **Implementar** en la barra de herramientas. Esta acción implementa la canalización en el servicio Azure Data Factory. 

### <a name="monitor-the-pipeline-run"></a>Supervisión de la ejecución de la canalización
La programación del conjunto de datos de salida se define en cada hora. La hora de finalización de la canalización es cinco horas después de la hora de inicio. Por lo tanto, verá cinco ejecuciones de canalización. 

1. Cierre las ventanas del editor para ver la página principal de la factoría de datos. Haga clic en el icono **Supervisión y administración**. 

    ![Icono Diagrama](./media/how-to-invoke-ssis-package-stored-procedure-activity/monitor-manage-tile.png)
2. Actualice **Hora de inicio** y **Hora de finalización** a **01/18/2018 08:30 AM** y **01/20/2018 08:30 AM**, y haga clic en **Aplicar**. Debería ver las **ventanas de actividad** asociadas a la ejecución de la canalización. 

    ![Ventanas de actividad](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-windows.png)

Para más información sobre la supervisión de canalizaciones, vea [Supervisión y administración de canalizaciones de Azure Data Factory mediante la aplicación de supervisión y administración](data-factory-monitor-manage-app.md).

## <a name="azure-powershell"></a>Azure PowerShell
En esta sección, se usa Azure PowerShell para crear una canalización de Data Factory con una actividad de procedimiento almacenado que invoca un paquete SSIS.

Instale los módulos de Azure PowerShell siguiendo las instrucciones de [Cómo instalar y configurar Azure PowerShell](/powershell/azure/install-azurerm-ps).

### <a name="create-a-data-factory"></a>Crear una factoría de datos
El siguiente procedimiento detalla los pasos para crear una factoría de datos. Debe crear una canalización con una actividad de procedimiento almacenado en esta factoría de datos. La actividad de procedimiento almacenado ejecuta un procedimiento almacenado en la base de datos SSISDB para ejecutar el paquete de SSIS.

1. Defina una variable para el nombre del grupo de recursos que usa en los comandos de PowerShell más adelante. Copie el texto del comando siguiente en PowerShell, especifique el nombre del [grupo de recursos de Azure](../../azure-resource-manager/resource-group-overview.md) entre comillas dobles y ejecute el comando. Por ejemplo: `"adfrg"`. 
   
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

5. Para crear la factoría de datos, ejecute el siguiente cmdlet **New-AzureRmDataFactory** con las propiedades ResourceGroupName y Location de la variable $ResGrp: 
    
    ```powershell       
    $df = New-AzureRmDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Tenga en cuenta los siguientes puntos:

* El nombre de la factoría de datos de Azure debe ser único global. Si recibe el siguiente error, cambie el nombre y vuelva a intentarlo.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Para crear instancias de Data Factory, la cuenta de usuario que use para iniciar sesión en Azure debe ser un miembro de los roles **colaborador** o **propietario**, o de **administrador** de la suscripción de Azure.

### <a name="create-an-azure-sql-database-linked-service"></a>Creación de un servicio vinculado a Azure SQL Database
Cree un servicio vinculado para vincular su instancia de Azure SQL Database que hospeda el catálogo de SSIS con la factoría de datos. Data Factory usa la información de este servicio vinculado para conectarse a la base de datos SSISDB y ejecuta un procedimiento almacenado para ejecutar un paquete de SSIS. 

1. Cree un archivo JSON denominado **AzureSQLDatabaseLinkedService.json** en la carpeta **C:\ADF\RunSSISPackage** con el siguiente contenido: 

    > [!IMPORTANT]
    > Reemplace &lt;servername&gt;, &lt;username&gt;, @&lt;servername&gt; y &lt;password&gt; por los nombres de su instancia de Azure SQL Database antes de guardar el archivo.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. En **Azure PowerShell**, cambie a la carpeta **C:\ADF\RunSSISPackage**.
3. Ejecute el cmdlet **New-AzureRmDataFactoryLinkedService** para crear el servicio vinculado: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzureRmDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Crear un conjunto de datos de salida
Este conjunto de datos de salida es un conjunto de datos ficticio que controla la programación de la canalización. Observe que el valor frequency está establecido en Hour y que interval está establecido en 1. Por lo tanto, la canalización se ejecuta una vez por hora entre las horas de inicio y fin de la canalización. 

1. Cree un archivo OuputDataset.json con el siguiente contenido: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Ejecute el cmdlet **New-AzureRmDataFactoryDataset** para crear un conjunto de datos. 

    ```powershell
    New-AzureRmDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Crear una canalización con una actividad de procedimiento almacenado 
En este paso, debe crear una canalización con una actividad de procedimiento almacenado. La actividad invoca el procedimiento almacenado sp_executesql para ejecutar su paquete de SSIS. 

1. Cree un archivo JSON con el nombre **MyPipeline.json** en la carpeta **C:\ADF\RunSSISPackage** con el siguiente contenido:

    > [!IMPORTANT]
    > Reemplace &lt;folder name&gt;, &lt;project name&gt; y &lt;package name&gt; por los nombres de carpeta, proyecto y paquete del catálogo de SSIS antes de guardar el archivo.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. Para crear la canalización **RunSSISPackagePipeline**, ejecute el cmdlet **New-AzureRmDataFactoryPipeline**.

    ```powershell
    $DFPipeLine = New-AzureRmDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Supervisión de la ejecución de la canalización

2. Ejecute **Get-AzureRmDataFactorySlice** para obtener la información sobre todos los segmentos del conjunto de datos de salida**, que es la tabla de salida de la canalización.

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Observe que la StartDateTime que especifique aquí es la misma hora de inicio especificada en el JSON de la canalización. 
3. Ejecute **Get-AzureRmDataFactoryRun** para más información de la actividad que se ejecuta para un segmento específico.

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Puede seguir ejecutando este cmdlet hasta que vea que el segmento se encuentra en el estado **Listo** o **Con error**. 

    Puede ejecutar la consulta siguiente en la base de datos SSISDB en el servidor de Azure SQL para comprobar la ejecución del paquete. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>pasos siguientes
Para obtener más información acerca de la actividad de procedimiento almacenado, consulte el artículo [Actividad de procedimiento almacenado](data-factory-stored-proc-activity.md).

