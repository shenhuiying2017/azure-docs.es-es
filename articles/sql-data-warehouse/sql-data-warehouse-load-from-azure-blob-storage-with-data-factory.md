---
redirect_url: /azure/sql-data-warehouse/sql-data-warehouse-load-with-data-factory
title: Carga de datos de Azure Blob Storage a Azure SQL Data Warehouse (Azure Data Factory) | Microsoft Docs
description: "Más información sobre Factoría de datos de Azure"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: 689fb02e-eb98-4f7c-81e6-6c1d22d53901
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 11/22/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: 4f8d038a606ce518d2c9ba232049ce5bea02dd4c


---
# <a name="load-data-from-azure-blob-storage-into-azure-sql-data-warehouse-azure-data-factory"></a>Carga de datos del Almacenamiento de blobs de Azure en Almacenamiento de datos SQL de Azure (Data Factory de Azure)
> [!div class="op_single_selector"]
> * [Factoría de datos](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
> * [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
> 
> 

 En este tutorial se muestra cómo crear una canalización en Factoría de datos de Azure para mover datos desde el Blob de almacenamiento de Azure a Almacenamiento de datos SQL. Con los siguiente pasos, hará lo siguiente:

* Configurar datos de ejemplo en un blob de Almacenamiento de Azure.
* Conectarse a recursos en Factoría de datos de Azure.
* Crear una canalización para mover datos de los blobs de Almacenamiento al Almacenamiento de datos SQL.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-Azure-SQL-Data-Warehouse-with-Azure-Data-Factory/player]
> 
> 

## <a name="before-you-begin"></a>Antes de empezar
Para familiarizarse con Azure Data Factory, vea [Introducción al servicio Azure Data Factory][Introduction to Azure Data Factory].

### <a name="create-or-identify-resources"></a>Creación o identificación de recursos
Antes de comenzar este tutorial, debe contar con los siguientes recursos.

* **Blob de Almacenamiento de Azure**: en este tutorial se usa el Blob de Almacenamiento de Azure como origen de datos para la canalización de Data Factory de Azure, así que debe tener uno disponible para almacenar los datos de ejemplo. Si todavía no tiene una, aprenda a [Crear una cuenta de almacenamiento][Create a storage account].
* **SQL Data Warehouse**: en este tutorial los datos se mueven desde Azure Storage Blob hasta SQL Data Warehouse, así que debe tener un almacén de datos en línea que tenga cargados los datos de ejemplo de AdventureWorksDW. Si no dispone de un almacén de datos, aprenda a [aprovisionar uno][Create a SQL Data Warehouse]. Si tiene un almacén de datos pero no lo ha aprovisionado con los datos de ejemplo, puede [cargarlo manualmente][Load sample data into SQL Data Warehouse].
* **Azure Data Factory**: Azure Data Factory realizará la carga en sí, así que necesita una instancia de Data Factory para crear la canalización del movimiento de datos. Si aún no tiene una, aprenda a crearla en el paso 1 de [Introducción a Azure Data Factory (Data Factory Editor)][Get started with Azure Data Factory (Data Factory Editor)].
* **AZCopy**: necesita AZCopy para copiar los datos de ejemplo desde el cliente local hasta el Blob de almacenamiento de Azure. Para conocer las instrucciones de instalación, consulte la [documentación de AZCopy][AZCopy documentation].

## <a name="step-1-copy-sample-data-to-azure-storage-blob"></a>Paso 1: Copia de los datos de ejemplo en el Blob de almacenamiento de Azure
Una vez que todos los componentes están listos, ya está preparado para copiar los datos de ejemplo en el Blob de almacenamiento de Azure.

1. [Descargue los datos de ejemplo][Download sample data]. Estos datos agregarán otros tres años de datos de ventas a los datos de ejemplo de AdventureWorksDW.
2. Utilice este comando de AZCopy para copiar los tres años de datos en el Blob de almacenamiento de Azure.

````
AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
````


## <a name="step-2-connect-resources-to-azure-data-factory"></a>Paso 2: Conexión de los recursos a Factoría de datos de Azure
Ahora que los datos están en su sitio podemos crear la canalización de Factoría de datos de Azure para mover los datos desde el almacenamiento de blobs de Azure a Almacenamiento de datos SQL.

Para empezar, abra [Azure Portal][Azure portal] y seleccione su instancia de Data Factory en el menú de la izquierda.

### <a name="step-21-create-linked-service"></a>Paso 2.1: Creación del servicio vinculado
Vincule la cuenta de Almacenamiento de Azure y Almacenamiento de datos SQL a la factoría de datos.  

1. En primer lugar, empiece el proceso de registro; para ello, haga clic en la sección "Servicios vinculados" de la factoría de datos y después haga clic en 'Nuevo almacén de datos'. Elija un nombre con el que registrar su almacenamiento de Azure, seleccione Almacenamiento de Azure como tipo y luego escriba el nombre y la clave de la cuenta.
2. Para registrar Almacenamiento de datos SQL, debe desplazarse hasta la sección 'Crear e implementar', luego seleccionar 'Nuevo almacén de datos' y, seguidamente, 'Almacenamiento de datos SQL de Azure'. Copie y pegue en esta plantilla y, a continuación, rellene su información específica.

```JSON
{
    "name": "<Linked Service Name>",
    "properties": {
        "description": "",
        "type": "AzureSqlDW",
        "typeProperties": {
             "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
         }
    }
}
```

### <a name="step-22-define-the-dataset"></a>Paso 2.2: Definición del conjunto de datos
Después de crear los servicios vinculados, debemos definir los conjuntos de datos.  Esto significa que hay que definir la estructura de los datos que se desplazan desde el almacenamiento al almacenamiento de datos.  Puede leer más sobre creación.

1. Inicie este proceso desplazándose a la sección 'Crear e implementar' de Factoría de datos.
2. Haga clic en 'Nuevo conjunto de datos' y después en 'Almacenamiento de blobs de Azure' para vincular el almacenamiento a Factoría de datos.  Puede usar el script siguiente para definir los datos de almacenamiento de blobs de Azure:

```JSON
{
    "name": "<Dataset Name>",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "<linked storage name>",
        "typeProperties": {
            "folderPath": "<containter name>",
            "fileName": "FactInternetSales.csv",
            "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```


1. Ahora definiremos también nuestro conjunto de datos para Almacenamiento de datos SQL.  Comenzamos en la misma forma, haciendo clic en 'Nuevo conjunto de datos' y después en 'Almacenamiento de datos SQL Azure'.

```JSON
{
    "name": "DWDataset",
    "properties": {
        "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "FactInternetSales"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

## <a name="step-3-create-and-run-your-pipeline"></a>Paso 3: Creación y ejecución de la canalización
Por último, vamos a configurar y ejecutar la canalización en Factoría de datos de Azure.  Se trata de la operación que completará el movimiento real de datos.  Puede encontrar una vista completa de las operaciones que se pueden completar con SQL Data Warehouse y Azure Data Factory [aquí][Move data to and from Azure SQL Data Warehouse using Azure Data Factory].

En la sección 'Crear e implementar', haga clic en 'Más comandos' y, a continuación, en 'Nueva canalización'.  Después de crear la canalización, puede usar el código siguiente para transferir los datos al almacenamiento de datos:

```JSON
{
    "name": "<Pipeline Name>",
    "properties": {
        "description": "<Description>",
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "skipHeaderLineCount": 1
                },
                "sink": {
                    "type": "SqlDWSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:10"
                }
            },
            "inputs": [
              {
                "name": "<Storage Dataset>"
              }
            ],
            "outputs": [
              {
                "name": "<Data Warehouse Dataset>"
              }
            ],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Sample Copy",
            "description": "Copy Activity"
          }
        ],
        "start": "<Date YYYY-MM-DD>",
        "end": "<Date YYYY-MM-DD>",
        "isPaused": false
    }
}
```

## <a name="next-steps"></a>Pasos siguientes
Para más información, vea lo siguiente para empezar:

* [Ruta de aprendizaje para Azure Data Factory][Azure Data Factory learning path].
* [Conector de Azure SQL Data Warehouse][Azure SQL Data Warehouse Connector]. Este es el tema de referencia principal para el uso de Data Factory de Azure con Almacenamiento de datos SQL de Azure.

En estos temas se proporciona información detallada sobre Data Factory de Azure. Se analiza Base de datos SQL de Azure o HDinsight, pero la información también se aplica a Almacenamiento de datos SQL de Azure.

* [Tutorial: Introducción a Azure Data Factory][Tutorial: Get started with Azure Data Factory] Este es el tutorial principal para el procesamiento de los datos con Azure Data Factory. En él aprenderá a crear su primera canalización que emplea HDInsight para transformar y analizar los registros web mensualmente. Tenga en cuenta que no hay ninguna actividad de copia en este tutorial.
* [Tutorial: Copia de datos de Azure Storage Blob en Azure SQL Database][Tutorial: Copy data from Azure Storage Blob to Azure SQL Database]. En este tutorial, creará una canalización en Factoría de datos de Azure para copiar datos desde el Blob de almacenamiento de Azure hasta Base de datos SQL Azure.

<!--Image references-->

<!--Article references-->
[AZCopy documentation]: ../storage/storage-use-azcopy.md
[Azure SQL Data Warehouse Connector]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[BCP]: sql-data-warehouse-load-with-bcp.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Create a storage account]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Data Factory]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Get started with Azure Data Factory (Data Factory Editor)]: ../data-factory/data-factory-build-your-first-pipeline-using-editor.md
[Introduction to Azure Data Factory]: ../data-factory/data-factory-introduction.md
[Load sample data into SQL Data Warehouse]: sql-data-warehouse-load-sample-databases.md
[Move data to and from Azure SQL Data Warehouse using Azure Data Factory]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[Tutorial: Copy data from Azure Storage Blob to Azure SQL Database]: ../data-factory/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[Tutorial: Get started with Azure Data Factory]: ../data-factory/data-factory-build-your-first-pipeline.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory learning path]: https://azure.microsoft.com/documentation/learning-paths/data-factory
[Azure portal]: https://portal.azure.com
[Download sample data]: https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv



<!--HONumber=Jan17_HO5-->


