---
title: "Carga de grandes cantidades de datos en Data Lake Store mediante el empleo de métodos sin conexión | Microsoft Docs"
description: Use la herramienta AdlCopy para copiar datos desde los blobs de Azure Storage a Data Lake Store.
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 45321f6a-179f-4ee4-b8aa-efa7745b8eb6
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 1309b44ea99af6d20a4d0f730dd68969f3c3082b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-data-lake-store"></a>Uso del servicio Azure Import/Export para la copia de datos sin conexión en Data Lake Store
En este artículo, aprenderá a copiar conjuntos de datos de gran tamaño (más de 200 GB) en Azure Data Lake Store mediante el empleo de métodos de copia sin conexión, como el [servicio Azure Import/Export](../storage/common/storage-import-export-service.md). En concreto, el archivo de ejemplo que utilizamos en este artículo tiene 339 420 860 416 bytes; es decir, ocupa unos 319 GB de espacio en disco. Llamaremos a este archivo "319GB.tsv".

El servicio Azure Import/Export le permite transferir de forma segura grandes cantidades de datos a Azure Blob Storage mediante el envío de unidades de disco duro a un centro de datos de Azure.

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar, debe disponer de lo siguiente:

* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* Una **cuenta de almacenamiento de Azure**.
* **Una cuenta de Almacén de Azure Data Lake**. Para obtener instrucciones sobre cómo crear una, consulte la [introducción al Almacén de Azure Data Lake](data-lake-store-get-started-portal.md)

## <a name="preparing-the-data"></a>Preparación de los datos
Antes de usar el servicio Import/Export, divida el archivo de datos para transferirlo **en copias de menos de 200 GB**. La herramienta de importación no funciona con archivos con un tamaño superior a 200 GB. En este tutorial, el archivo se divide en fragmentos de 100 GB. Puede hacerlo si utiliza [Cygwin](https://cygwin.com/install.html). Cygwin admite comandos de Linux. En este caso, ejecute el siguiente comando:

    split -b 100m 319GB.tsv

La operación de división crea archivos con los nombres siguientes.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Preparación de los discos con datos
Siga las instrucciones de [Uso del servicio Azure Import/Export para transferir datos a Azure Storage](../storage/common/storage-import-export-service.md) (sección **Preparación de las unidades**) para preparar los discos duros. Aquí se muestra la secuencia general:

1. Adquiera un disco duro que cumpla los requisitos para poder utilizarse con el servicio Azure Import/Export.
2. Identifique una cuenta de Azure Storage donde vayan a copiarse los datos cuando se envíen al centro de datos de Azure.
3. Utilice la [herramienta Azure Import/Export](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), una utilidad de línea de comandos. Abajo, podrá encontrar un fragmento de código de ejemplo que muestra cómo utilizar la herramienta.

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    Consulte [Uso del servicio Azure Import/Export](../storage/common/storage-import-export-service.md) para obtener más fragmentos de código de ejemplo.
4. El comando anterior crea un archivo de diario en la ubicación especificada. Use este archivo de diario para crear un trabajo de importación desde el [Portal de Azure clásico](https://manage.windowsazure.com).

## <a name="create-an-import-job"></a>Crear un trabajo de importación
Ahora puede crear un trabajo de importación con las instrucciones de [Uso del servicio Azure Import/Export ](../storage/common/storage-import-export-service.md) (sección **Creación de un trabajo de importación**). Para este trabajo de importación, proporcione también el archivo de diario creado al preparar las unidades de disco, además de otros detalles.

## <a name="physically-ship-the-disks"></a>Envío físico de los discos
Ahora puede enviar físicamente los discos a un centro de datos de Azure. Allí, se copiarán los datos a los blobs de Azure Storage que proporcionó al crear el trabajo de importación. Además, al crear el trabajo, si eligió proporcionar la información de seguimiento más adelante, ahora podrá volver al trabajo de importación y actualizar el número de seguimiento.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-store"></a>Copia de datos de blobs de Azure Storage en Azure Data Lake Store
Cuando se muestre el estado del trabajo de importación como completado, podrá comprobar si los datos están disponibles en los blobs de Azure Storage que había especificado. Después, podrá utilizar diversos métodos para mover estos datos de los blobs de Azure Storage a Azure Data Lake Store. Para ver todas las opciones disponibles para cargar datos, consulte el artículo sobre cómo [ingerir datos en Data Lake Store](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-store).

En esta sección, proporcionamos las definiciones de JSON que puede usar para crear una canalización de Azure Data Factory con el objetivo de copiar datos. Puede utilizar estas definiciones de JSON en [Azure Portal](../data-factory/v1/data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](../data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](../data-factory/v1/data-factory-copy-activity-tutorial-using-powershell.md).

### <a name="source-linked-service-azure-storage-blob"></a>Servicio vinculado de origen (blob de Azure Storage)
````
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
````

### <a name="target-linked-service-azure-data-lake-store"></a>Servicio vinculado de destino (Azure Data Lake Store)
````
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Store with your access rights>",
            "dataLakeStoreUri": "https://<adls_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
````
### <a name="input-data-set"></a>Conjunto de datos de entrada
````
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
````
### <a name="output-data-set"></a>Conjunto de datos de salida
````
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStoreLinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
````
### <a name="pipeline-copy-activity"></a>Canalización (actividad de copia)
````
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
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
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
````
Para más información, consulte [Movimiento de datos hacia y desde Azure Data Lake Store mediante Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-azure-data-lake-store"></a>Reconstrucción de los archivos de datos de Azure Data Lake Store
Comenzamos con un archivo de 319 GB y lo dividimos en fragmentos de menor tamaño para que se pudiesen transferir mediante el servicio Azure Import/Export. Ahora que los datos están en Azure Data Lake Store, podemos reconstruir el archivo para que vuelva a tener su tamaño original. También puede utilizar el siguiente cmdlet de Azure PowerShell.

````
# Login to our account
Login-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext –SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adls_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv”
````

## <a name="next-steps"></a>Pasos siguientes
* [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
* [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
