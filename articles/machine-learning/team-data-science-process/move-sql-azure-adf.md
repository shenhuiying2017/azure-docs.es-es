---
title: Movimiento de datos desde un servidor SQL Server local hasta SQL Azure con Azure Data Factory de Azure | Microsoft Docs
description: "Configure una canalización de ADF que componga dos actividades de migración de datos que se combinen para mover datos diariamente entre bases de datos locales y de nube."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 36837c83-2015-48be-b850-c4346aa5ae8a
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2017
ms.author: bradsev
ms.openlocfilehash: fed2e9af3e9765ce5a2486fe9468d3ca690a0d5d
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/20/2018
---
# <a name="move-data-from-an-on-premises-sql-server-to-sql-azure-with-azure-data-factory"></a>Movimiento de datos desde un servidor SQL Server local hasta SQL Azure con Azure Data Factory
En este tema se muestra cómo mover datos desde una base de datos local de SQL Server hasta una base de datos de SQL Azure a través de Azure Blob Storage mediante Azure Data Factory (ADF).

Para ver una tabla que resuma varias opciones para mover datos a Azure SQL Database, vea [Mover datos a Azure SQL Database para Azure Machine Learning](move-sql-azure.md).

## <a name="intro"></a>Introducción: ¿qué es la ADF y cuándo se debe usar para migrar datos?
La Azure Data Factory es un servicio de integración de datos totalmente administrado basado en la nube que organiza y automatiza el movimiento y la transformación de datos. El concepto clave en el modelo de ADF es la canalización. Una canalización es una agrupación lógica de actividades, cada una de las cuales define las acciones que se deben realizar en los datos incluidos en los conjuntos de datos. Los servicios vinculados se usan para definir la información necesaria para que la Factoría de datos se conecte a los recursos de datos.

Con la ADF, los servicios de procesamiento de datos existentes se pueden componer en canalizaciones de datos altamente disponibles y administradas en la nube. Estas canalizaciones de datos se pueden programar para ingerir, preparar, transformar, analizar y publicar datos, mientras que ADF administra y organiza los datos complejos y las dependencias de procesamiento. Las soluciones se pueden generar e implementar rápidamente en la nube. Para ello, se conecta una cantidad en aumento de orígenes de datos locales y de nube.

Considere el uso de ADF en estos casos:

* Cuando los datos deban migrarse continuamente en un escenario híbrido que tiene acceso a recursos locales y en la nube.
* cuando los datos se transfieren o deben modificarse o tener lógica empresarial agregada mientras se migran.

La ADF permite la programación y supervisión de trabajos mediante scripts JSON sencillos que administran el movimiento de datos de forma periódica. La ADF también tiene otras capacidades como la compatibilidad con operaciones complejas. Para obtener más información sobre la ADF, vea la documentación de [Azure Data Factory (ADF)](https://azure.microsoft.com/services/data-factory/).

## <a name="scenario"></a>Escenario
Configuramos una canalización ADF que se compone de dos actividades de migración de datos. En conjunto, mueven datos a diario entre una instancia de SQL Database local y otra instancia de Azure SQL Database en la nube. Las dos actividades son:

* copiar datos de una base de datos SQL Server local a una cuenta de Azure Blob Storage.
* copiar datos de la cuenta de Azure Blob Storage a una Azure SQL Database.

> [!NOTE]
> Los pasos que aparecen aquí se han adaptado de un tutorial más detallado que ofrece el equipo de ADF: [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md). Las referencias a las secciones pertinentes de ese tema se darán cuando corresponda.
>
>

## <a name="prereqs"></a>Requisitos previos
En este tutorial se asume que dispone de:

* Una **suscripción de Azure**. Si no tiene una suscripción, puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Una **cuenta de almacenamiento de Azure**. En este tutorial se usa una cuenta de Azure Storage para almacenar los datos. Si no dispone de una cuenta de almacenamiento de Azure, vea el artículo [Creación de una cuenta de almacenamiento](../../storage/common/storage-create-storage-account.md#create-a-storage-account) . Tras crear la cuenta de almacenamiento, tendrá que obtener la clave de cuenta que se usa para tener acceso al almacenamiento. Consulte [Administración de las claves de acceso de almacenamiento](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Acceso a **Azure SQL Database**. Si debe configurar una instancia de Azure SQL Database, la [introducción a Azure SQL Database](../../sql-database/sql-database-get-started.md) proporciona información sobre cómo aprovisionar una nueva instancia de Azure SQL Database.
* **Azure PowerShell** instalado y configurado de forma local. Para obtener instrucciones, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).

> [!NOTE]
> En este procedimiento se usa [Azure Portal](https://portal.azure.com/).
>
>

## <a name="upload-data"></a> Carga de datos en la instancia de SQL Server local
Usamos el [conjunto de datos de taxis de Nueva York](http://chriswhong.com/open-data/foil_nyc_taxi/) para demostrar el proceso de migración. El conjunto de datos de taxis de Nueva York está disponible, como se especificó en esa publicación, en la instancia de Azure Blob Storage [NYC Taxi Data](http://www.andresmh.com/nyctaxitrips/). Los datos tienen dos archivos, el archivo trip_data.csv, que contiene detalles de carreras, y el archivo trip_far.csv, que contiene detalles de la tarifa de cada carrera. En [Descripción del conjunto de datos de carreras de taxi de Nueva York](sql-walkthrough.md#dataset), se proporciona un ejemplo y una descripción de estos archivos.

Puede adaptar el procedimiento que se proporciona aquí para un conjunto de datos propios o seguir los pasos descritos para el uso del conjunto de datos de taxis de Nueva York. Para cargar el conjunto de datos de taxis de Nueva York en la base de datos de SQL Server local, siga el procedimiento descrito en [Importación masiva de datos en una base de datos de SQL Server](sql-walkthrough.md#dbload). Estas instrucciones son para un servidor SQL Server en una máquina virtual de Azure, pero el procedimiento para realizar la carga en el servidor SQL Server local es el mismo.

## <a name="create-adf"></a> Crear una factoría de datos de Azure
Las instrucciones para crear una nueva factoría de datos de Azure y un grupo de recursos en [Azure Portal](https://portal.azure.com/) se proporcionan en [Creación de Data Factory](../../data-factory/v1/data-factory-build-your-first-pipeline-using-editor.md#create-a-data-factory). Ponga el nombre *adfdsp* a la nueva instancia de ADF y el nombre *adfdsprg* al grupo de recursos creado.

## <a name="install-and-configure-up-the-data-management-gateway"></a>Instalar y configurar Data Management Gateway
Para permitir que las canalizaciones de una factoría de datos de Azure funcionen con un servidor SQL Server local, debe agregar el servidor como un servicio vinculado a la factoría de datos. Para crear un servicio vinculado para un servidor SQL Server local, debe hacer lo siguiente:

* Descargue e instale Microsoft Data Management Gateway en el equipo local.
* Configure el servicio vinculado para el origen de datos local para utilizar la puerta de enlace.

Data Management Gateway serializa y deserializa los datos de origen y receptor en el equipo donde se hospeda.

Para obtener instrucciones de instalación e información detallada sobre Data Management Gateway, consulte [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md)

## <a name="adflinkedservices"></a>Crear servicios vinculados para conectarse a los recursos de datos
Un servicio vinculado define la información necesaria para que Azure Data Factory se conecte a un recurso de datos. Tenemos tres recursos en este escenario para los que se necesitan servicios vinculados:

1. SQL Server local
2. Azure Blob Storage
3. Azure SQL Database

El procedimiento paso a paso para crear servicios vinculados se proporciona en [Crear servicios vinculados](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-linked-services).


## <a name="adf-tables"></a>Definir y crear tablas para especificar cómo tener acceso a los conjuntos de datos
Cree tablas que especifiquen la estructura, la ubicación y la disponibilidad de los conjuntos de datos con los siguientes procedimientos de scripts. Los archivos JSON se usan para definir las tablas. Para obtener más información sobre la estructura de estos archivos, vea [Conjuntos de datos](../../data-factory/v1/data-factory-create-datasets.md).

> [!NOTE]
> Debe ejecutar el cmdlet `Add-AzureAccount` antes de ejecutar el cmdlet [New-AzureDataFactoryTable](https://msdn.microsoft.com/library/azure/dn835096.aspx) para confirmar que se selecciona la suscripción de Azure adecuada para la ejecución del comando. Para obtener documentación sobre este cmdlet, consulte [Add-AzureAccount](/powershell/module/azure/add-azureaccount?view=azuresmps-3.7.0).
>
>

Las definiciones basadas en JSON de las tablas usan los siguientes nombres:

* el **nombre de la tabla** de SQL Server local es *nyctaxi_data*
* el **nombre del contenedor** de la cuenta de Azure Blob Storage es *containername*  

Se necesitan tres definiciones de tabla para esta canalización de ADF:

1. [Tabla de SQL local](#adf-table-onprem-sql)
2. [Tabla Blob ](#adf-table-blob-store)
3. [Tabla SQL de Azure](#adf-table-azure-sql)

> [!NOTE]
> Estos procedimientos usan Azure PowerShell para definir y crear las actividades de ADF. Sin embargo, estas tareas también se pueden realizar en Azure Portal. Para más información, consulte [Creación de conjuntos de datos](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-datasets).
>
>

### <a name="adf-table-onprem-sql"></a>Tabla de SQL local
La definición de tabla de SQL Server local se especifica en el siguiente archivo JSON:

        {
            "name": "OnPremSQLTable",
            "properties":
            {
                "location":
                {
                "type": "OnPremisesSqlServerTableLocation",
                "tableName": "nyctaxi_data",
                "linkedServiceName": "adfonpremsql"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1,   
                "waitOnExternal":
                {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
                }

                }
            }
        }

Los nombres de columna no se incluyeron aquí. Puede subseleccionar los nombres de columna incluyéndolos aquí (para obtener detalles, vea la [documentación de ADF](../../data-factory/v1/data-factory-data-movement-activities.md) ).

Copie la definición de JSON de la tabla en un archivo denominado *onpremtabledef.json* y guarde el archivo en una ubicación conocida (aquí se supone que es *C:\temp\onpremtabledef.json*). Cree la tabla en ADF con el siguiente cmdlet de Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp –File C:\temp\onpremtabledef.json


### <a name="adf-table-blob-store"></a>Tabla Blob
La definición de la tabla para la ubicación del blob de salida se encuentra en la siguiente ubicación (este esquema asigna los datos ingeridos de una ubicación local a un blob de Azure):

        {
            "name": "OutputBlobTable",
            "properties":
            {
                "location":
                {
                "type": "AzureBlobLocation",
                "folderPath": "containername",
                "format":
                {
                "type": "TextFormat",
                "columnDelimiter": "\t"
                },
                "linkedServiceName": "adfds"
                },
                "availability":
                {
                "frequency": "Day",
                "interval": 1
                }
            }
        }

Copie la definición de JSON de la tabla en un archivo denominado *bloboutputtabledef.json* y guarde el archivo en una ubicación conocida (aquí se supone que es *C:\temp\bloboutputtabledef.json*). Cree la tabla en ADF con el siguiente cmdlet de Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\bloboutputtabledef.json  

### <a name="adf-table-azure-sq"></a>Tabla SQL de Azure
La definición de la tabla para la salida SQL de Azure se encuentra en la siguiente ubicación (este esquema asigna los datos procedentes del blob):

    {
        "name": "OutputSQLAzureTable",
        "properties":
        {
            "structure":
            [
                { "name": "column1", type": "String"},
                { "name": "column2", type": "String"}                
            ],
            "location":
            {
                "type": "AzureSqlTableLocation",
                "tableName": "your_db_name",
                "linkedServiceName": "adfdssqlazure_linked_servicename"
            },
            "availability":
            {
                "frequency": "Day",
                "interval": 1            
            }
        }
    }

Copie la definición de JSON de la tabla en un archivo denominado *AzureSqlTable.json* y guarde el archivo en una ubicación conocida (aquí se supone que es *C:\temp\AzureSqlTable.json*). Cree la tabla en ADF con el siguiente cmdlet de Azure PowerShell:

    New-AzureDataFactoryTable -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\AzureSqlTable.json  


## <a name="adf-pipeline"></a>Definición y creación de la canalización
Especifique las actividades que pertenecen a la canalización y cree la canalización con los siguientes procedimientos de scripts. Un archivo JSON se usa para definir las propiedades de la canalización.

* En el script se supone que el **nombre de la canalización** es *AMLDSProcessPipeline*.
* Además, tenga en cuenta que la periodicidad de la canalización que se va a ejecutar se establece como diaria y que se usa el tiempo de ejecución predeterminado para el trabajo (12 a.m. UTC).

> [!NOTE]
> Los siguientes procedimientos usan Azure PowerShell para definir y crear la canalización de ADF. Sin embargo, esta tarea también se puede realizar en Azure Portal. Para más información, consulte [Creación de una canalización](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-pipeline).
>
>

Mediante las definiciones de tabla proporcionadas anteriormente, la definición de la canalización para ADF se especifica de la siguiente manera:

        {
            "name": "AMLDSProcessPipeline",
            "properties":
            {
                "description" : "This pipeline has one Copy activity that copies data from an on-premises SQL to Azure blob",
                 "activities":
                [
                    {
                        "name": "CopyFromSQLtoBlob",
                        "description": "Copy data from on-premises SQL server to blob",     
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OnPremSQLTable"} ],
                        "outputs": [ {"name": "OutputBlobTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "SqlSource",
                                "sqlReaderQuery": "select * from nyctaxi_data"
                            },
                            "sink":
                            {
                                "type": "BlobSink"
                            }   
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 0,
                            "timeout": "01:00:00"
                        }       

                     },

                    {
                        "name": "CopyFromBlobtoSQLAzure",
                        "description": "Push data to Sql Azure",        
                        "type": "CopyActivity",
                        "inputs": [ {"name": "OutputBlobTable"} ],
                        "outputs": [ {"name": "OutputSQLAzureTable"} ],
                        "transformation":
                        {
                            "source":
                            {                               
                                "type": "BlobSource"
                            },
                            "sink":
                            {
                                "type": "SqlSink",
                                "WriteBatchTimeout": "00:5:00",                
                            }            
                        },
                        "Policy":
                        {
                            "concurrency": 3,
                            "executionPriorityOrder": "NewestFirst",
                            "style": "StartOfInterval",
                            "retry": 2,
                            "timeout": "02:00:00"
                        }
                     }
                ]
            }
        }

Copie esta definición de JSON de la canalización en un archivo denominado *pipelinedef.json* y guarde el archivo en una ubicación conocida (aquí se supone que es *C:\temp\pipelinedef.json*). Cree la canalización en ADF con el siguiente cmdlet de Azure PowerShell:

    New-AzureDataFactoryPipeline  -ResourceGroupName adfdsprg -DataFactoryName adfdsp -File C:\temp\pipelinedef.json


## <a name="adf-pipeline-start"></a>Inicio de la canalización
La canalización ya se puede ejecutar mediante el comando siguiente:

    Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFdsprg -DataFactoryName ADFdsp -StartDateTime startdateZ –EndDateTime enddateZ –Name AMLDSProcessPipeline

Los valores de los parámetros *startdate* y *enddate* deben reemplazarse por las fechas reales entre las que desea que se ejecute la canalización.

Una vez que se ejecuta la canalización, debe poder ver los datos que aparecen en el contenedor seleccionado para el blob, en este caso, un archivo por día.

Observe que no hemos aprovechado la funcionalidad que proporciona la ADF para canalizar los datos de forma incremental. Para más información sobre cómo hacer esto y sobre otras funcionalidades que proporciona ADF, consulte la [documentación de ADF](https://azure.microsoft.com/services/data-factory/).
