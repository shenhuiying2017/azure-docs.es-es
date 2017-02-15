---
title: Actividad de streaming de Hadoop
description: "Aprenda cómo puede usar la actividad de streaming de Hadoop en una factoría de datos de Azure para ejecutar programas de streaming de Hadoop en un clúster de HDInsight suyo propio o a petición."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2016
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: a4121f8857fa9eaeb1cf1bca70e29666f6a04f63
ms.openlocfilehash: 58c0e0cfd862bc421c3b4a9ad042283a22656460


---
# <a name="hadoop-streaming-activity"></a>Actividad de streaming de Hadoop
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> * [Aprendizaje automático](data-factory-azure-ml-batch-execution-activity.md) 
> * [Procedimiento almacenado](data-factory-stored-proc-activity.md)
> * [U-SQL de análisis con Data Lake](data-factory-usql-activity.md)
> * [.NET personalizado](data-factory-use-custom-activities.md)

Puede usar la actividad HDInsightStreamingActivity para invocar un trabajo de streaming de Hadoop desde una canalización de Factoría de datos de Azure. El siguiente fragmento de código JSON muestra la sintaxis para usar HDInsightStreamingActivity en un archivo JSON de canalización. 

La actividad de streaming de HDInsight en una [canalización](data-factory-create-pipelines.md) de Data Factory ejecuta programas de streaming de Hadoop en un clúster de HDInsight basado en Windows/Linux [propio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o en uno [a petición](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Este artículo se basa en el artículo sobre [actividades de transformación de datos](data-factory-data-transformation-activities.md) , que presenta información general de la transformación de datos y las actividades de transformación admitidas.

## <a name="json-sample"></a>Ejemplo de JSON
El clúster de HDInsight se rellena automáticamente con los programas de ejemplo (wc.exe y cat.exe) y los datos (davinci.txt). De forma predeterminada, el nombre del contenedor usado por el clúster de HDInsight es el nombre del propio clúster. Por ejemplo, si el nombre del clúster es myhdicluster, el nombre del contenedor de blobs asociado sería myhdicluster. 

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<nameofthecluster>/example/apps/wc.exe",
                        "<nameofthecluster>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00Z",
        "end": "2014-01-05T00:00:00Z"
    }
}
```

Tenga en cuenta los siguientes puntos:

1. Establezca **linkedServiceName** en el nombre del servicio vinculado que apunta al clúster de HDInsight en el que se va a ejecutar el trabajo de MapReduce de streaming.
2. Establezca el tipo de la actividad en **HDInsightStreaming**.
3. Para la propiedad **mapper** , especifique el nombre del ejecutable del asignador. En el ejemplo, cat.exe es el ejecutable del asignador.
4. Para la propiedad **reducer** , especifique el nombre del ejecutable del reductor. En el ejemplo, wc.exe es el ejecutable del reductor.
5. Para la propiedad **input** type, especifique el archivo de entrada (incluida la ubicación) para el asignador. En el ejemplo: "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample es el contenedor de blobs, example/data/Gutenberg es la carpeta y davinci.txt es el blob.
6. Para la propiedad **output** type, especifique el archivo de salida (incluida la ubicación) para el reductor. La salida del trabajo de streaming de Hadoop se escribirá en la ubicación especificada para esta propiedad.
7. En la sección **filePaths** , especifique las rutas de acceso para los archivos ejecutables del asignador y del reductor. En el ejemplo: "adfsample/example/apps/wc.exe", adfsample es el contenedor de blobs, example/apps es la carpeta y wc.exe es el ejecutable.
8. Para la propiedad **fileLinkedService** , especifique el servicio vinculado de Almacenamiento de vinculados que representa el almacenamiento de Azure que contiene los archivos especificados en la sección filePaths.
9. Para la propiedad **arguments** , especifique los argumentos para el trabajo de streaming.
10. La propiedad **getDebugInfo** es un elemento opcional. Cuando se establece en Failure, los registros solo se descargan en caso de error. Cuando se establece en All, los registros se descargan siempre, sea cual sea el estado de ejecución.

> [!NOTE]
> Como se muestra en el ejemplo, deberá especificar un conjunto de datos de salida para la actividad de streaming de Hadoop en la propiedad **outputs** . Este conjunto de datos es simplemente un conjunto de datos ficticio que es necesario para la programación de la canalización. No es necesario especificar ningún conjunto de datos de entrada para la actividad de la propiedad **inputs** .  
> 
> 

## <a name="example"></a>Ejemplo
La canalización de este tutorial ejecuta el programa de asignación/reducción de transmisión por secuencias del recuento de palabras en el clúster de HDInsight de Azure. 

### <a name="linked-services"></a>Servicios vinculados
#### <a name="azure-storage-linked-service"></a>Servicio vinculado de Almacenamiento de Azure
En primer lugar, cree un servicio vinculado para vincular el almacenamiento de Azure usado por el clúster de HDInsight de Azure con la Factoría de datos de Azure. Si copia/pega el código siguiente, no olvide reemplazar el nombre y la clave de la cuenta por el nombre y la clave de su Almacenamiento de Azure. 

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Servicio vinculado de HDInsight de Azure
A continuación, cree un servicio vinculado para vincular el clúster de HDInsight de Azure con la Factoría de datos de Azure. Si copia/pegar el código siguiente, reemplace el nombre del clúster de HDInsight por el nombre de su clúster de HDInsight y cambie los valores de nombre de usuario y contraseña. 

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>Conjuntos de datos
#### <a name="output-dataset"></a>Conjunto de datos de salida
La canalización de este ejemplo no toma ninguna entrada. Deberá especificar un conjunto de datos de salida para la actividad de streaming de HDInsight. Este conjunto de datos es simplemente un conjunto de datos ficticio que es necesario para la programación de la canalización. 

```JSON
{
    "name": "StreamingOutputDataset",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "adftutorial/streamingdata/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Canalización
La canalización de este ejemplo tiene solo una actividad de tipo: **HDInsightStreaming**. 

El clúster de HDInsight se rellena automáticamente con los programas de ejemplo (wc.exe y cat.exe) y los datos (davinci.txt). De forma predeterminada, el nombre del contenedor usado por el clúster de HDInsight es el nombre del propio clúster. Por ejemplo, si el nombre del clúster es myhdicluster, el nombre del contenedor de blobs asociado sería myhdicluster.  

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<blobcontainer>/example/apps/wc.exe",
                        "<blobcontainer>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "StorageLinkedService"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00Z",
        "end": "2017-01-04T00:00:00Z"
    }
}
```
## <a name="see-also"></a>Otras referencias
* [Actividad de Hive](data-factory-hive-activity.md)
* [Actividad de Pig](data-factory-pig-activity.md)
* [Actividad MapReduce](data-factory-map-reduce.md)
* [Invocar programas Spark](data-factory-spark.md)
* [Invocar scripts de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)




<!--HONumber=Nov16_HO3-->


