---
title: Copia de datos de forma incremental con Azure Data Factory | Microsoft Docs
description: "En este tutorial, creará una canalización de Azure Data Factory que copia datos de forma incremental de Azure SQL Database a Azure Blob Storage."
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
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: 91b632b6d2c2917acf17e9d89c1b5a4b0f8b1c33
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage"></a>Carga de datos de forma incremental de Azure SQL Database a Azure Blob Storage
Azure Data Factory es un servicio de integración de datos basado en la nube que le permite crear flujos de trabajo basados en datos en la nube a fin de coordinar y automatizar el movimiento y la transformación de datos. Mediante Azure Data Factory, puede crear y programar flujos de trabajo orientados a datos (llamados canalizaciones) que pueden ingerir datos de almacenes de datos dispares, procesar o transformar los datos mediante servicios de proceso, como Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics y Azure Machine Learning, y publicar datos de salida en almacenes de datos, como Azure SQL Data Warehouse para que los consuman las aplicaciones de inteligencia empresarial (BI). 

Durante el recorrido de integración de datos, uno de los escenarios ampliamente usados consiste en cargar de forma incremental los datos periódicamente para actualizar el resultado de los análisis actualizados después del análisis y las cargas de datos iniciales. En este tutorial, se centrará en cargar únicamente registros nuevos o actualizados desde los orígenes de datos en los receptores de datos. Esto resulta más eficiente comparado con las completas, especialmente para los conjuntos de datos de gran tamaño.    

Puede usar Data Factory para crear soluciones con límite máximo a fin de lograr la carga de datos incremental mediante actividades de búsqueda, copia y procedimiento almacenado en una canalización.  

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Preparación del almacén de datos para almacenar el valor de marca de agua   
> * Creación de una factoría de datos.
> * Cree servicios vinculados. 
> * Creación de los conjuntos de datos de marca de agua, de origen y del receptor
> * Creación de una canalización
> * Ejecución de la canalización
> * Supervisión de la ejecución de la canalización 

## <a name="overview"></a>Información general
El diagrama de solución de alto nivel es: 

![Cargar datos de forma incremental](media\tutorial-Incrementally-load-data-from-azure-sql-to-blob\incrementally-load.png)

Estos son los pasos importantes para crear esta solución: 

1. **Seleccione la columna de marca de agua**.
    Seleccione una columna en el almacén de datos de origen, que pueda usarse para segmentar los registros nuevos o actualizados para cada ejecución. Normalmente, los datos de esta columna seleccionada (por ejemplo, last_modify_time o id.) siguen aumentando cuando se crean o se actualizan las filas. El valor máximo de esta columna se utiliza como una marca de agua.
2. **Prepare el almacén de datos para almacenar el valor de marca de agua**.   
    En este tutorial, el valor de marca de agua se almacena en una instancia de Azure SQL Database.
3. **Cree una canalización con el siguiente flujo de trabajo:** 
    
    La canalización de esta solución consta de las siguientes actividades:
  
    1. Cree dos actividades de **búsqueda**. Use la primera actividad de búsqueda para recuperar el último valor de marca de agua y, la segunda actividad, para recuperar el nuevo valor de marca de agua. Estos valores de marca de agua se pasan a la actividad de copia. 
    2. Cree una **actividad de copia** que copie filas del almacén de datos de origen con un valor de columna de marca de agua que sea mayor al valor anterior e inferior al nuevo. A continuación, copia las diferencias de datos del almacén de datos de origen en un almacenamiento de blob como un archivo nuevo. 
    3. Cree una **actividad de procedimiento almacenado** que actualice el valor de marca de agua de la canalización que se ejecute la próxima vez. 


Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos
* **Base de datos de SQL Azure**. La base de datos se usa como almacén de datos de **origen**. Si no tiene ninguna instancia de Azure SQL Database, consulte el artículo [Creación de una instancia de Azure SQL Database](../sql-database/sql-database-get-started-portal.md) para ver los pasos para su creación.
* **Cuenta de Almacenamiento de Azure**. Blob Storage se usa como almacén de datos **receptor**. Si no tiene ninguna cuenta de almacenamiento de Azure, consulte la sección [Crear una cuenta de almacenamiento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para ver los pasos para su creación. Cree un contenedor denominado **adftutorial**. 
* **Azure PowerShell**. Siga las instrucciones de [Instalación y configuración de Azure PowerShell](/powershell/azure/install-azurerm-ps).

### <a name="create-a-data-source-table-in-your-azure-sql-database"></a>Creación de una tabla de origen de datos en Azure SQL Database
1. Abra **SQL Server Management Studio**, en el **Explorador de servidores**, haga clic con el botón derecho en la base de datos y elija la **Nueva consulta**.
2. Ejecute el siguiente comando SQL en Azure SQL Database para crear una tabla denominada `data_source_table` como almacén de origen de datos.  
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    En este tutorial, utilizará **LastModifytime** como columna de **marca de agua**.  Los datos del almacén de origen de datos se muestran en la tabla siguiente:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-sql-database-to-store-the-high-watermark-value"></a>Creación de otra tabla en la base de datos SQL para almacenar el valor de límite máximo
1. Ejecute el siguiente comando SQL en Azure SQL Database para crear una tabla denominada `watermarktable` y almacenar el valor de marca de agua.  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
3. Establezca el **valor** predeterminado de límite máximo con el nombre de la tabla del almacén de datos de origen.  (En este tutorial, el nombre de tabla es **data_source_table**).

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
4. Revise los datos de la tabla: `watermarktable`.
    
    ```sql
    Select * from watermarktable
    ```
    Salida: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-azure-sql-database"></a>Creación de un procedimiento almacenado en Azure SQL Database 

Ejecute el siguiente comando para crear un procedimiento almacenado en Azure SQL Database.

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

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
    Set-AzureRmDataFactoryV2 -ResourceGroupName "<your resource group to create the factory>" -Location "East US" -Name "<specify the name of data factory to create. It must be globally unique.>" 
    ```

    Tenga en cuenta los siguientes puntos:

    * El nombre del generador de datos de Azure debe ser único global. Si recibe el siguiente error, cambie el nombre y vuelva a intentarlo.

        ```
        The specified Data Factory name '<data factory name>' is already in use. Data Factory names must be globally unique.
        ```

    * Para crear instancias de Data Factory, debe ser administrador o colaborador en la suscripción de Azure.
    * Actualmente, Data Factory V2 solo le permite crear una factoría de datos en la región Este de EE. UU. Los almacenes de datos (Azure Storage, Azure SQL Database, etc.) y los procesos (HDInsight, etc.) que usa la factoría de datos pueden encontrarse en otras regiones.


## <a name="create-linked-services"></a>Crear servicios vinculados
Los servicios vinculados se crean en una factoría de datos para vincular los almacenes de datos y los servicios de proceso con la factoría de datos. En esta sección, creará servicios vinculados para su cuenta de Azure Storage y Azure SQL Database. 

### <a name="create-azure-storage-linked-service"></a>Creación de un servicio vinculado de Azure Storage
1. Cree un archivo JSON llamado **AzureStorageLinkedService.json** en la carpeta **C:\ADF** con el siguiente contenido: (cree la carpeta ADF si aún no existe). Reemplace `<accountName>`, `<accountKey>` por el nombre y la clave de su cuenta de Azure Storage antes de guardar el archivo.

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
2. En **Azure PowerShell**, cambie a la carpeta **ADF**.
3. Ejecute el cmdlet **Set-AzureRmDataFactoryV2LinkedService** para crear el servicio vinculado: **AzureStorageLinkedService**. En el ejemplo siguiente, debe pasar los valores de los parámetros **ResourceGroupName** y **DataFactoryName**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Este es la salida de ejemplo:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-azure-sql-database-linked-service"></a>Creación de un servicio vinculado a Azure SQL Database
1. Cree un archivo JSON llamado **AzureSQLDatabaseLinkedService.json** en la carpeta **C:\ADF** con el siguiente contenido: (cree la carpeta ADF si aún no existe). Reemplace **&lt;server&gt;, &lt;user id&gt; y &lt;password&gt;** por el nombre de su servidor SQL Server de Azure, su id. de usuario y su contraseña antes de guardar el archivo. 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name> ; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. En **Azure PowerShell**, cambie a la carpeta **ADF**.
3. Ejecute el cmdlet **Set-AzureRmDataFactoryV2LinkedService** para crear el servicio vinculado: **AzureSQLDatabaseLinkedService**. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Este es la salida de ejemplo:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ProvisioningState :
    ```

## <a name="create-datasets"></a>Creación de conjuntos de datos
En este paso, debe crear conjuntos de datos que representan los datos de origen y del receptor. 

### <a name="create-a-source-dataset"></a>Creación de un conjunto de datos de origen

1. Cree un archivo JSON llamado SourceDataset.json en la misma carpeta con el siguiente contenido: 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
   
    ```
    En este tutorial, el nombre de tabla es **data_source_table**). Reemplácelo si está utilizando una tabla con otro nombre. 
2.  Ejecute el cmdlet Set-AzureRmDataFactoryV2Dataset para crear el conjunto de datos: SourceDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Esta es la salida de ejemplo del cmdlet:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Creación de un conjunto de datos receptor

1. Cree un archivo JSON llamado SinkDataset.json en la misma carpeta con el siguiente contenido: 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incrementalcopy",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')", 
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

    > [!IMPORTANT]
    > Con este fragmento de código se da por supuesto que tiene un contenedor de blob denominado **adftutorial** en Azure Blob Storage. Cree el contenedor si no existe (o) asígnele el nombre de uno existente. La carpeta de salida `incrementalcopy` se crea automáticamente si no existe en el contenedor. En este tutorial, el nombre de archivo se genera dinámicamente mediante la expresión: `@CONCAT('Incremental-', pipeline().RunId, '.txt')`.
2.  Ejecute el cmdlet Set-AzureRmDataFactoryV2Dataset para crear el conjunto de datos: SinkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Esta es la salida de ejemplo del cmdlet:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset    
    ```

## <a name="create-a-dataset-for-watermark"></a>Creación de un conjunto de datos de marca de agua
En este paso, creará un conjunto de datos para almacenar un valor de límite máximo. 

1. Cree un archivo JSON llamado WatermarkDataset.json en la misma carpeta con el siguiente contenido: 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2.  Ejecute el cmdlet Set-AzureRmDataFactoryV2Dataset para crear el conjunto de datos: WatermarkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Esta es la salida de ejemplo del cmdlet:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Crear una canalización
En este tutorial, creará una canalización con dos actividades de búsqueda, una actividad de copia y una actividad de procedimiento almacenado encadenado a una canalización. 


1. Cree un archivo JSON llamado IncrementalCopyPipeline.json en la misma carpeta con el siguiente contenido. 

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupOldWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from watermarktable"
                        },
    
                        "dataset": {
                        "referenceName": "WatermarkDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupNewWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select MAX(LastModifytime) as NewWatermarkvalue from data_source_table"
                        },
    
                        "dataset": {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                
                {
                    "name": "IncrementalCopyActivity",
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupNewWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupOldWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ],
    
                    "inputs": [
                        {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "SinkDataset",
                            "type": "DatasetReference"
                        }
                    ]
                },
    
                {
                    "name": "StoredProceduretoWriteWatermarkActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
    
                        "storedProcedureName": "sp_write_watermark",
                        "storedProcedureParameters": {
                            "LastModifiedtime": {"value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}", "type": "datetime" },
                            "TableName":  { "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}", "type":"String"}
                        }
                    },
    
                    "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
    
                    "dependsOn": [
                        {
                            "activity": "IncrementalCopyActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ],
            
        }
    }
    ```json

    If you are using a source table with a name different from the one used in the tutorial (**data_source_table**), replace **data_source_table** in the sqlReaderQuery with the name of your source table. 
    

2. Run the Set-AzureRmDataFactoryV2Pipeline cmdlet to create the pipeline: IncrementalCopyPipeline.
    
   ```powershell
   Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Este es la salida de ejemplo: 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Activities        : {LookupOldWaterMarkActivity, LookupNewWaterMarkActivity, IncrementalCopyActivity, StoredProceduretoWriteWatermarkActivity}
    Parameters        :
   ```
 
## <a name="run-the-pipeline"></a>Ejecución de la canalización

1. Ejecute la canalización **IncrementalCopyPipeline** mediante el cmdlet **Invoke-AzureRmDataFactoryV2Pipeline**. Reemplace los marcadores de posición con su propio grupo de recursos y el nombre de la factoría de datos.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup "<your resource group>" -dataFactoryName "<your data factory name>"
    ``` 
2. Compruebe el estado de la canalización mediante el cmdlet Get-AzureRmDataFactoryV2ActivityRun hasta que vea que todas las actividades se ejecutan correctamente. Reemplace los marcadores de posición con la hora que le resulte adecuada para el parámetro RunStartedAfter y RunStartedBefore.  En este tutorial, usamos -RunStartedAfter "2017/09/14" -RunStartedBefore "2017/09/15"

    ```powershell
    Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Este es la salida de ejemplo:
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:42:50 AM
    DurationInMs      : 7777
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:43:07 AM
    DurationInMs      : 25437
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:10 AM
    ActivityRunEnd    : 9/14/2017 7:43:29 AM
    DurationInMs      : 19769
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:32 AM
    ActivityRunEnd    : 9/14/2017 7:43:47 AM
    DurationInMs      : 14467
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```

## <a name="review-the-results"></a>Revisión del resultado

1. En Azure Blob Storage (almacén de receptor), verá que los datos se han copiado en el archivo definido en SinkDataset.  En el tutorial actual, el nombre de archivo es `Incremental- d4bf3ce2-5d60-43f3-9318-923155f61037.txt`.  Abra el archivo y podrá ver que los registros del archivo son los mismos que los datos de Azure SQL Database.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ``` 
2. Compruebe el ultimo valor de `watermarktable`. Verá que el valor de marca de agua se ha actualizado.

    ```sql
    Select * from watermarktable
    ```
    
    Este es la salida de ejemplo:
 
    TableName | WatermarkValue
    --------- | --------------
    data_source_table   2017-09-05  8:06:00.000

### <a name="insert-data-into-data-source-store-to-verify-delta-data-loading"></a>Inserción de datos en el almacén de origen de datos para comprobar la carga de datos delta

1. Inserte nuevos datos en Azure SQL Database (almacén de origen de datos):

    ```sql
    INSERT INTO data_source_table
    VALUES (6, 'newdata','9/6/2017 2:23:00 AM')
    
    INSERT INTO data_source_table
    VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
    ``` 

    Los datos actualizados en Azure SQL Database son los siguientes:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    6 | newdata | 2017-09-06 02:23:00.000
    7 | newdata | 2017-09-07 09:01:00.000
    ```
2. Ejecute de nuevo la canalización **IncrementalCopyPipeline** mediante el cmdlet **Invoke-AzureRmDataFactoryV2Pipeline**. Reemplace los marcadores de posición con su propio grupo de recursos y el nombre de la factoría de datos.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup "<your resource group>" -dataFactoryName "<your data factory name>"
    ```
3. Compruebe el estado de la canalización mediante el cmdlet **Get-AzureRmDataFactoryV2ActivityRun** hasta que vea que todas las actividades se ejecutan correctamente. Reemplace los marcadores de posición con la hora que le resulte adecuada para el parámetro RunStartedAfter y RunStartedBefore.  En este tutorial, usamos -RunStartedAfter "2017/09/14" -RunStartedBefore "2017/09/15"

    ```powershell
    Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Este es la salida de ejemplo:
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:58 AM
    DurationInMs      : 31758
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:52 AM
    DurationInMs      : 25497
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:00 AM
    ActivityRunEnd    : 9/14/2017 8:53:20 AM
    DurationInMs      : 20194
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:23 AM
    ActivityRunEnd    : 9/14/2017 8:53:41 AM
    DurationInMs      : 18502
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```
4.  En Azure Blob Storage, debería ver que se ha creado otro archivo en Azure Blob Storage. En este tutorial, el nombre de archivo nuevo es `Incremental-2fc90ab8-d42c-4583-aa64-755dba9925d7.txt`.  Abra el archivo y verá que contiene 2 registros de filas:
5.  Compruebe el ultimo valor de `watermarktable`. Observará que el valor de marca de agua se ha vuelvo a actualizar.

    ```sql
    Select * from watermarktable
    ```
    salida de ejemplo: 
    
    TableName | WatermarkValue
    --------- | ---------------
    data_source_table | 2017-09-07 09:01:00.000

     
## <a name="next-steps"></a>Pasos siguientes
En este tutorial, realizó los pasos siguientes: 

> [!div class="checklist"]
> * Definición de una columna de **marca de agua** y almacenamiento de esta en Azure SQL Database.  
> * Creación de una factoría de datos.
> * Creación de servicios vinculados para SQL Database y Blob Storage 
> * Creación de conjuntos de datos de origen y receptores.
> * Creación de una canalización
> * Ejecución de la canalización
> * Supervisión de la ejecución de la canalización 

Pase al tutorial siguiente para obtener información acerca de la transformación de datos mediante el uso de un clúster de Spark en Azure:

> [!div class="nextstepaction"]
>[Transformar datos con un clúster de Spark en la nube](tutorial-transform-data-spark-powershell.md)



