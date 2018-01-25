---
title: Copia incremental de varias tablas mediante Azure Data Factory | Microsoft Docs
description: "En este tutorial, creará una canalización de Azure Data Factory que copia los datos diferenciales de forma incremental de varias tablas de una base de datos local de SQL Server a una base de datos SQL de Azure."
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
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: 9eeb265e063e6642b90dd641d41d0a54cbc6951e
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Carga incremental de datos de varias tablas de SQL Server a Azure SQL Database
En este tutorial, creará una factoría de datos de Azure con una canalización que carga los datos diferenciales de varias tablas de una instancia local de SQL Server a una base de datos SQL de Azure.    

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Preparación de los almacenes de datos de origen y de destino
> * Creación de una factoría de datos.
> * Cree una instancia de Integration Runtime autohospedada.
> * Instalación del entorno de ejecución de integración 
> * Cree servicios vinculados. 
> * Creación de conjuntos de datos de marca de agua, de origen y de receptor.
> * Creación, ejecución y supervisión de una canalización
> * Consulte los resultados.
> * Adición o actualización de datos en tablas de origen
> * Nueva ejecución y supervisión de la canalización
> * Revisión de los resultados finales

> [!NOTE]
> Este artículo se aplica a la versión 2 de Azure Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte la [documentación de la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Información general
Estos son los pasos importantes para crear esta solución: 

1. **Seleccione la columna de marca de agua**.
    Seleccione una columna de cada tabla del almacén de datos de origen, que pueda usarse para identificar los registros nuevos o actualizados de cada ejecución. Normalmente, los datos de esta columna seleccionada (por ejemplo, last_modify_time o id.) siguen aumentando cuando se crean o se actualizan las filas. El valor máximo de esta columna se utiliza como una marca de agua.

2. **Prepare el almacén de datos para almacenar el valor de marca de agua**.   
    En este tutorial, el valor de marca de agua se almacena en una base de datos SQL.

3. **Cree una canalización con las siguientes actividades:** 
    
    a. Cree una actividad ForEach que recorra en iteración una lista de nombres de tabla de origen que se pase como parámetro a la canalización. Para cada tabla de origen, invoca las siguientes actividades para realizar la carga diferencial de esa tabla.

    b. Cree dos actividades de búsqueda. Use la primera actividad de búsqueda para recuperar el último valor de marca de agua. y, la segunda actividad, para recuperar el nuevo valor de marca de agua. Estos valores de marca de agua se pasan a la actividad de copia.

    c. Cree una actividad de copia que copie filas del almacén de datos de origen con el valor de la columna de marca de agua que sea mayor que el valor anterior y menor que el nuevo. A continuación, copie los datos diferenciales del almacén de datos de origen a Azure Blob Storage como un archivo nuevo.

    d. Cree un procedimiento almacenado que actualice el valor de marca de agua de la canalización que se ejecute la próxima vez. 

    Este es el diagrama de solución de alto nivel: 

    ![Cargar datos de forma incremental](media\tutorial-incremental-copy-multiple-tables-powershell\high-level-solution-diagram.png)


Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>requisitos previos
* **SQL Server**. En este tutorial, usará una base de datos local SQL Server como almacén de datos de origen. 
* **Azure SQL Database**. Usará una base de datos SQL como almacén de datos receptor. Si no tiene ninguna, consulte [Creación de una instancia de Azure SQL Database](../sql-database/sql-database-get-started-portal.md) para ver los pasos para su creación. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Creación de tablas de origen en la base de datos de SQL Server

1. Abra SQL Server Management Studio y conéctese a la base de datos SQL Server local.

2. En el **Explorador de servidores**, haga clic con el botón derecho en la base de datos y elija **Nueva consulta**.

3. Ejecute el siguiente comando SQL en la base de datos para crear las tablas denominadas `customer_table` y `project_table`:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-sql-database"></a>Creación de las tablas de destino en la base de datos SQL
1. Abra SQL Server Management Studio y conéctese a la base de datos SQL Server.

2. En el **Explorador de servidores**, haga clic con el botón derecho en la base de datos y elija **Nueva consulta**.

3. Ejecute el siguiente comando SQL en la base de datos SQL para crear las tablas denominadas `customer_table` y `project_table`:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-sql-database-to-store-the-high-watermark-value"></a>Creación de otra tabla en la base de datos SQL para almacenar el valor de límite máximo
1. Ejecute el siguiente comando SQL en la base de datos SQL para crear una tabla denominada `watermarktable` y almacenar el valor de marca de agua: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
2. Inserte los valores iniciales de marca de agua de ambas tablas de origen en la tabla de marcas de agua.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-sql-database"></a>Creación de un procedimiento almacenado en la base de datos SQL 

Ejecute el siguiente comando para crear un procedimiento almacenado en la base de datos SQL. Este procedimiento almacenado actualiza el valor de la marca de agua después de cada ejecución de canalización. 

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures"></a>Creación de tipos de datos y procedimientos almacenados adicionales
Ejecute la consulta siguiente para crear dos procedimientos almacenados y dos tipos de datos en la base de datos SQL. Estos procedimientos se usan para combinar los datos de las tablas de origen en las tablas de destino.

```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE sp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE sp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

### <a name="azure-powershell"></a>Azure PowerShell
Instale los módulos más recientes de Azure PowerShell siguiendo las instrucciones de [Cómo instalar y configurar Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-data-factory"></a>Crear una factoría de datos
1. Defina una variable para el nombre del grupo de recursos que usa en los comandos de PowerShell más adelante. Copie el texto del comando siguiente en PowerShell, especifique el nombre del [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) entre comillas dobles y ejecute el comando. Un ejemplo es `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Si el grupo de recursos ya existe, puede que no desee sobrescribirlo. Asigne otro valor a la variable `$resourceGroupName` y vuelva a ejecutar el comando.

2. Defina una variable para la ubicación de la factoría de datos. 

    ```powershell
    $location = "East US"
    ```
3. Para crear el grupo de recursos de Azure, ejecute el comando siguiente: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Si el grupo de recursos ya existe, puede que no desee sobrescribirlo. Asigne otro valor a la variable `$resourceGroupName` y vuelva a ejecutar el comando.

4. Defina una variable para el nombre de la factoría de datos. 

    > [!IMPORTANT]
    >  Actualice el nombre de la factoría de datos para que sea globalmente único. Por ejemplo, ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
5. Para crear la factoría de datos, ejecute el siguiente cmdlet, **Set-AzureRmDataFactoryV2**: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Tenga en cuenta los siguientes puntos:

* El nombre de la factoría de datos debe ser globalmente único. Si recibe el siguiente error, cambie el nombre y vuelva a intentarlo:

    ```
    The specified Data Factory name 'ADFIncMultiCopyTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Para crear instancias de Data Factory, la cuenta de usuario que use para iniciar sesión en Azure debe ser un miembro de los roles colaborador o propietario, o de administrador de la suscripción de Azure.
* Actualmente, la versión 2 de Data Factory permite crear factorías de datos solo en las regiones Este de EE. UU., Este de EE. UU. 2 y Europa Occidental. Los almacenes de datos (Azure Storage, SQL Database, etc.) y los procesos (Azure HDInsight, etc.) que usa la factoría de datos pueden encontrarse en otras regiones.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]



## <a name="create-linked-services"></a>Crear servicios vinculados
Los servicios vinculados se crean en una factoría de datos para vincular los almacenes de datos y los servicios de proceso con la factoría de datos. En esta sección, creará servicios vinculados a la base de datos local SQL Server y a la base de datos SQL. 

### <a name="create-the-sql-server-linked-service"></a>Creación del servicio vinculado de SQL Server
En este paso, vinculará la base de datos SQL Server local a la factoría de datos.

1. Cree un archivo JSON llamado SqlServerLinkedService.json en la carpeta C:\ADFTutorials\IncCopyMultiTableTutorial con el contenido siguiente. Seleccione la sección adecuada según la autenticación que use para conectarse a SQL Server. Cree las carpetas locales si no existen. 

    > [!IMPORTANT]
    > Seleccione la sección adecuada según la autenticación que use para conectarse a SQL Server.

    Si usa la autenticación de SQL, copie la siguiente definición JSON:

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
    Si usa la autenticación de Windows, copie la siguiente definición de JSON:

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<server>;Database=<database>;Integrated Security=True"
                },
                "userName": "<user> or <domain>\\<user>",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
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
    > [!IMPORTANT]
    > - Seleccione la sección adecuada según la autenticación que use para conectarse a SQL Server.
    > - Reemplace &lt;nombre del entorno de ejecución de integración> por el nombre de su entorno de ejecución de integración.
    > - Antes de guardar el archivo, reemplace los valores de &lt;nombre del servidor>, &lt;nombre de base de datos>, &lt;nombre de usuario> y &lt;contraseña> por los de la base de datos SQL Server.
    > - Si necesita usar un carácter de barra diagonal (`\`) en el nombre de servidor o en la cuenta de usuario, utilice el carácter de escape (`\`). Un ejemplo es `mydomain\\myuser`.

2. En PowerShell, cambie a la carpeta C:\ADFTutorials\IncCopyMultiTableTutorial.

3. Ejecute el cmdlet **Set-AzureRmDataFactoryV2LinkedService** para crear el servicio vinculado AzureStorageLinkedService. En el ejemplo siguiente, debe pasar los valores de los parámetros *ResourceGroupName* y *DataFactoryName*: 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerLinkedService" -File ".\SqlServerLinkedService.json"
    ```

    Este es la salida de ejemplo:

    ```json
    LinkedServiceName : SqlServerLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerLinkedService
    ```

### <a name="create-the-sql-database-linked-service"></a>Creación del servicio vinculado de SQL Database
1. Cree un archivo JSON llamado AzureSQLDatabaseLinkedService.json en la carpeta C:\ADFTutorials\IncCopyMultiTableTutorial con el contenido siguiente. (Cree la carpeta ADF si no existe). Antes de guardar el archivo, remplace los valores de &lt;servidor&gt;, &lt;nombre de base de datos&gt;, &lt;id. de usuario&gt; y &lt;contraseña&gt; por los de su instancia de SQL Server Database. 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name>; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. En PowerShell, ejecute el cmdlet **Set-AzureRmDataFactoryV2LinkedService** para crear el servicio vinculado AzureSQLDatabaseLinkedService. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Este es la salida de ejemplo:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Creación de conjuntos de datos
En este paso, creará conjuntos de datos para representar el origen de datos, el destino de los datos y el lugar para almacenar la marca de agua.

### <a name="create-a-source-dataset"></a>Creación de un conjunto de datos de origen

1. Cree un archivo JSON llamado SourceDataset.json en la misma carpeta con el siguiente contenido: 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dummyName"
            },
            "linkedServiceName": {
                "referenceName": "SqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
   
    ```

    El nombre de tabla es un nombre ficticio. La actividad de copia en la canalización usa una consulta SQL para cargar los datos en lugar de cargar la tabla entera.

2. Ejecute el cmdlet **Set-AzureRmDataFactoryV2Dataset** para crear el conjunto de datos SourceDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Esta es la salida de ejemplo del cmdlet:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Creación de un conjunto de datos receptor

1. Cree un archivo JSON llamado SinkDataset.json en la misma carpeta con el siguiente contenido. El elemento tableName se establece dinámicamente mediante la canalización en tiempo de ejecución. La actividad ForEach de la canalización recorre en iteración una lista de nombres de tabla y pasa el nombre de tabla a este conjunto de datos en cada iteración. 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().SinkTableName}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "SinkTableName": {
                    "type": "String"
                }
            }
        }
    }
    ```

2. Ejecute el cmdlet **Set-AzureRmDataFactoryV2Dataset** para crear el conjunto de datos SinkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Esta es la salida de ejemplo del cmdlet:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-a-watermark"></a>Creación de un conjunto de datos para una marca de agua
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
2. Ejecute el cmdlet **Set-AzureRmDataFactoryV2Dataset** para crear el conjunto de datos WatermarkDataset.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Esta es la salida de ejemplo del cmdlet:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : <data factory name>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Crear una canalización
La canalización toma una lista de tablas como un parámetro. La actividad ForEach recorre en iteración la lista de nombres de tabla y realiza las siguientes operaciones: 

1. Usa la actividad de búsqueda para recuperar el valor de marca de agua antiguo (valor inicial o que se usó en la última iteración).

2. Usa la actividad de búsqueda para recuperar el nuevo valor de marca de agua (valor máximo de la columna de marca de agua en la tabla de origen).

3. Usa la actividad de copia para copiar datos entre estos dos valores de marca de agua desde la base de datos de origen a la base de datos de destino.

4. Usa el procedimiento almacenado para actualizar el valor de marca de agua antiguo que se usará en el primer paso de la iteración siguiente. 

### <a name="create-the-pipeline"></a>Creación de la canalización
1. Cree un archivo JSON llamado IncrementalCopyPipeline.json en la misma carpeta con el siguiente contenido: 

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [{
    
                "name": "IterateSQLTables",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "false",
                    "items": {
                        "value": "@pipeline().parameters.tableList",
                        "type": "Expression"
                    },
    
                    "activities": [
                        {
                            "name": "LookupOldWaterMarkActivity",
                            "type": "Lookup",
                            "typeProperties": {
                                "source": {
                                    "type": "SqlSource",
                                    "sqlReaderQuery": "select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'"
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
                                    "sqlReaderQuery": "select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}"
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
                                    "sqlReaderQuery": "select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                                },
                                "sink": {
                                    "type": "SqlSink",
                                    "SqlWriterTableType": "@{item().TableType}",
                                    "SqlWriterStoredProcedureName": "@{item().StoredProcedureNameForMergeOperation}"
                                }
                            },
                            "dependsOn": [{
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
    
                            "inputs": [{
                                "referenceName": "SourceDataset",
                                "type": "DatasetReference"
                            }],
                            "outputs": [{
                                "referenceName": "SinkDataset",
                                "type": "DatasetReference",
                                "parameters": {
                                    "SinkTableName": "@{item().TABLE_NAME}"
                                }
                            }]
                        },
    
                        {
                            "name": "StoredProceduretoWriteWatermarkActivity",
                            "type": "SqlServerStoredProcedure",
                            "typeProperties": {
    
                                "storedProcedureName": "sp_write_watermark",
                                "storedProcedureParameters": {
                                    "LastModifiedtime": {
                                        "value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}",
                                        "type": "datetime"
                                    },
                                    "TableName": {
                                        "value": "@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}",
                                        "type": "String"
                                    }
                                }
                            },
    
                            "linkedServiceName": {
                                "referenceName": "AzureSQLDatabaseLinkedService",
                                "type": "LinkedServiceReference"
                            },
    
                            "dependsOn": [{
                                "activity": "IncrementalCopyActivity",
                                "dependencyConditions": [
                                    "Succeeded"
                                ]
                            }]
                        }
    
                    ]
    
                }
            }],
    
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```
2. Ejecute el cmdlet **Set-AzureRmDataFactoryV2Pipeline** para crear la canalización IncrementalCopyPipeline.
    
   ```powershell
   Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Este es la salida de ejemplo: 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```
 
## <a name="run-the-pipeline"></a>Ejecución de la canalización

1. Cree un archivo de parámetros llamado Parameters.json en la misma carpeta con el siguiente contenido:

    ```json
    {
        "tableList": 
        [
            {
                "TABLE_NAME": "customer_table",
                "WaterMark_Column": "LastModifytime",
                "TableType": "DataTypeforCustomerTable",
                "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
            },
            {
                "TABLE_NAME": "project_table",
                "WaterMark_Column": "Creationtime",
                "TableType": "DataTypeforProjectTable",
                "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
            }
        ]
    }
    ```
2. Ejecute la canalización IncrementalCopyPipeline mediante el cmdlet **Invoke-AzureRmDataFactoryV2Pipeline**. Reemplace los marcadores de posición por su propio grupo de recursos y el nombre de la factoría de datos.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>Supervisar la canalización

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. Haga clic en **Más servicios**, busque con la palabra clave *Factorías de datos* y seleccione **Factorías de datos**. 

    ![Menú Factorías de datos](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-data-factories-menu-1.png)

3. Busque su factoría de datos en la lista y selecciónela para abrir la página **Factoría de datos**. 

    ![Búsqueda de la factoría de datos](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-search-data-factory-2.png)

4. En la página **Factoría de datos**, seleccione **Supervisión y administración**. 

    ![Icono Supervisión y administración](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-monitor-manage-tile-3.png)

5. La **aplicación de integración de datos** se abre en otra pestaña. Puede ver todas las ejecuciones de canalización y sus estados. Tenga en cuenta que, en el ejemplo siguiente, el estado de ejecución de la canalización es **Correcto**. Para comprobar los parámetros pasados a la canalización, seleccione el vínculo en la columna **Parámetros**. Si se ha producido un error, verá un vínculo en la columna **Error**. Seleccione el vínculo en la columna **Acciones**. 

    ![Ejecuciones de la canalización](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-4.png)    
6. Al seleccionar el vínculo de la columna **Acciones**, verá la página siguiente que muestra todas las ejecuciones de actividad de la canalización: 

    ![Ejecuciones de actividad](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-activity-runs-5.png)

7. Para volver a la vista de **ejecuciones de canalización**, haga clic en **Canalizaciones** como se muestra en la imagen. 

## <a name="review-the-results"></a>Revisión del resultado
En SQL Server Management Studio, ejecute las consultas siguientes contra la base de datos SQL de Azure de destino para comprobar que los datos se copiaron de las tablas de origen a las tablas de destino: 

**Consultar** 
```sql
select * from customer_table
```

**Salida**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Consultar**

```sql
select * from project_table
```

**Salida**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Consultar**

```sql
select * from watermarktable
```

**Salida**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Observe que se actualizaron los valores de marca de agua de ambas tablas. 

## <a name="add-more-data-to-the-source-tables"></a>Adición de más datos a las tablas de origen

Ejecute la consulta siguiente contra la base de datos SQL Server de origen para actualizar una fila existente en customer_table. Inserte una nueva fila en project_table. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>Nueva ejecución de la canalización

1. Ahora, vuelva a ejecutar la canalización mediante el siguiente comando de PowerShell:

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupname -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"
    ```
2. Supervise las ejecuciones de canalización siguiendo las instrucciones de la sección [Supervisión de la canalización](#monitor-the-pipeline). Como el estado de la canalización es **En curso**, verá otro vínculo de acción en **Acciones** para cancelar la ejecución de canalización. 

    ![Ejecuciones de canalización En curso](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-6.png)

3. Haga clic en **Actualizar** para actualizar la lista hasta que la ejecución de canalización se realice correctamente. 

    ![Actualización de ejecuciones de canalización](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-succeded-7.png)

4. De manera opcional, seleccione el vínculo **View Activity Runs** (Ver ejecuciones de actividad) en **Acciones** para ver todas las ejecuciones de actividad asociadas a esta ejecución de canalización. 

## <a name="review-the-final-results"></a>Revisión de los resultados finales
En SQL Server Management Studio, ejecute las siguientes consultas contra la base de datos de destino para comprobar que los datos nuevos o actualizados se han copiado de las tablas de origen a las tablas de destino. 

**Consultar** 
```sql
select * from customer_table
```

**Salida**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

Observe los nuevos valores de **Nombre** y **LastModifytime** de **PersonID**: 3. 

**Consultar**

```sql
select * from project_table
```

**Salida**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

Tenga en cuenta que la entrada **NewProject** se agregó a project_table. 

**Consultar**

```sql
select * from watermarktable
```

**Salida**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Observe que se actualizaron los valores de marca de agua de ambas tablas.
     
## <a name="next-steps"></a>pasos siguientes
En este tutorial, realizó los pasos siguientes: 

> [!div class="checklist"]
> * Preparación de los almacenes de datos de origen y de destino
> * Creación de una factoría de datos.
> * Creación de una instancia de Integration Runtime (IR) autohospedado
> * Instalación del entorno de ejecución de integración
> * Cree servicios vinculados. 
> * Creación de conjuntos de datos de marca de agua, de origen y de receptor.
> * Creación, ejecución y supervisión de una canalización
> * Consulte los resultados.
> * Adición o actualización de datos en tablas de origen
> * Nueva ejecución y supervisión de la canalización
> * Revisión de los resultados finales

Pase al tutorial siguiente para obtener información acerca de la transformación de datos mediante el uso de un clúster de Spark en Azure:

> [!div class="nextstepaction"]
>[Incrementally load data from Azure SQL Database to Azure Blob Storage using Change Tracking technology](tutorial-incremental-copy-change-tracking-feature-powershell.md) (Carga incremental de datos de Azure SQL Database a Azure Blob Storage mediante la tecnología de control de cambios)


