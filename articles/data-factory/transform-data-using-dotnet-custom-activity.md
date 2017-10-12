---
title: "Uso de actividades personalizadas en una canalización de Factoría de datos de Azure"
description: "Obtenga información acerca de cómo crear actividades personalizadas y usarlas en una canalización de la factoría de datos de Azure."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: shengc
ms.openlocfilehash: 24f15168fd716cf317087b8a2ad19b66574ce569
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Uso de actividades personalizadas en una canalización de Factoría de datos de Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-use-custom-activities.md)
> * [Versión 2: Versión preliminar](transform-data-using-dotnet-custom-activity.md)

Hay dos tipos de actividades que puede usar en una canalización de Data Factory de Azure.

- [Actividades de movimiento de datos](copy-activity-overview.md) para mover datos entre [almacenes de datos de origen y receptor compatibles](copy-activity-overview.md#supported-data-stores-and-formats).
- [Actividades de transformación de datos](transform-data.md) para transformar datos mediante procesos como Azure HDInsight, Azure Batch y Azure Machine Learning. 

Para mover datos desde y hacia un almacén de datos incompatible con Data Factory, o para transformar o procesar datos de algún modo incompatible con Data Factory, puede crear una **actividad personalizada** con su propia lógica de desplazamiento o transformación de datos y usarla en una canalización. La actividad personalizada ejecuta la lógica del código personalizado en un grupo de máquinas virtuales de **Azure Batch**.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en la versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea [Custom activities in V1](v1/data-factory-use-custom-activities.md) (Actividades personalizadas en V1).
 

Consulte los temas siguientes si no está familiarizado con el servicio Lote de Azure:

* [Aspectos básicos de Lote de Azure](../batch/batch-technical-overview.md) para información general del servicio Lote de Azure.
* Cmdlet [New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) para crear una cuenta de Azure Batch o [Azure Portal](../batch/batch-account-create-portal.md) para crear la cuenta de Azure Batch con Azure Portal. Consulte el tema [Uso de Azure PowerShell para administrar la cuenta de Lote de Azure](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) para obtener instrucciones detalladas sobre cómo usar este cmdlet.
* [New-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) para crear un grupo de Lote de Azure.

## <a name="azure-batch-linked-service"></a>Servicio vinculado de Azure Batch 
El siguiente JSON define un servicio de línea de Azure Batch de ejemplo. Para obtener información detallada, vea [Compute environments supported by Azure Data Factory](compute-linked-services.md) (Entornos de proceso compatibles con Azure Data Factory).

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

 Para más información sobre el servicio vinculado de Azure Batch, vea el artículo [Servicios vinculados de proceso](compute-linked-services.md). 

## <a name="custom-activity"></a>Actividad personalizada

El siguiente fragmento de código JSON define una canalización con una actividad personalizada simple. La definición de actividad tiene una referencia al servicio vinculado de Azure Batch. 

```json
{
    "name": "MyCustomActivityPipeline",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "helloworld.exe",
          "folderPath": "customactv2/helloworld",
          "resourceLinkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }]
    }
  }
```

En este ejemplo, el archivo helloworld.exe es una aplicación personalizada que se almacena en la carpeta customactv2/helloworld de la cuenta de Azure Storage usada en la propiedad resourceLinkedService. La actividad personalizada envía esta aplicación personalizada para ejecutarla en Azure Batch. Puede reemplazar el comando en cualquier aplicación preferida que se pueda ejecutar en el sistema operativo de destino de los nodos del grupo de Azure Batch. 

En la tabla siguiente se describen los nombres y descripciones de las propiedades que son específicas de esta actividad. 

| Propiedad              | Descripción                              | Obligatorio |
| :-------------------- | :--------------------------------------- | :------- |
| name                  | Nombre de la actividad en la canalización     | Sí      |
| Descripción           | Texto que describe para qué se usa la actividad.  | No       |
| type                  | Para la actividad personalizada, el tipo de actividad es **Custom**. | Sí      |
| linkedServiceName     | Servicio vinculado a Azure Batch. Para obtener más información sobre este servicio vinculado, vea el artículo [Compute linked services](compute-linked-services.md) (Servicios vinculados de procesos).  | Sí      |
| command               | Comando de la aplicación personalizada que se va a ejecutar. Si la aplicación ya está disponible en el nodo del grupo de Azure Batch, se pueden omitir las propiedades resourceLinkedService y folderPath. Por ejemplo, puede especificar que el comando sea `cmd /c dir`, que el nodo del grupo de lotes de Windows admite de forma nativa. | Sí      |
| resourceLinkedService | Servicio de Azure Storage vinculado a la cuenta de almacenamiento en la que está almacenada la aplicación personalizada | No       |
| folderPath            | Ruta de acceso a la carpeta de la aplicación personalizada y todas sus dependencias | No       |
| referenceObjects      | Matriz de servicios vinculados y conjuntos de datos existentes. Los servicios vinculados y los conjuntos de datos a los que se hace referencia se pasan a la aplicación personalizada en formato JSON, por lo que el código personalizado puede hacer referencia a recursos de Data Factory | No       |
| extendedProperties    | Propiedades definidas por el usuario que se pueden pasar a la aplicación personalizada en formato JSON, por lo que el código personalizado puede hacer referencia a propiedades adicionales | No       |

## <a name="passing-objects-and-properties"></a>Pasar objetos y propiedades

En este ejemplo se muestra cómo usar las propiedades referenceObjects y extendedProperties para pasar objetos de Data Factory y propiedades definidas por el usuario a la aplicación personalizada. 


```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "aSampleSecureString"
            }           
        }
      }
    }]
  }
}
```

Al ejecutar la actividad, las propiedades referenceObjects y extendedProperties se almacenan en los siguientes archivos que se implementan en la misma carpeta de ejecución del archivo SampleApp.exe: 

- activity.json

  Almacena extendedProperties y las propiedades de la actividad personalizada. 

- linkedServices.json

  Almacena una matriz de servicios vinculados definidos en la propiedad referenceObjects. 

- datasets.json

  Almacena una matriz de conjuntos de datos definidos en la propiedad referenceObjects. 

En el siguiente código de ejemplo se muestra cómo SampleApp.exe puede obtener acceso a la información necesaria de los archivos JSON: 

```C#
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.connectionString.value);
        }
    }
}
```

####<a name="retrieve-execution-outputs"></a>Recuperar los resultados de la ejecución

Puede iniciar una ejecución de la canalización de ejemplo y supervisar el resultado de la ejecución con los siguientes comandos de PowerShell: 

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -dataFactoryName "factoryName" -PipelineName "pipelineName" 
$result = Get-AzureRmDataFactoryV2ActivityRun -dataFactoryName "factoryName" -PipelineRunId $runId -RunStartedAfter "2017-09-06" -RunStartedBefore "2017-12-31"
$result.output -join "`r`n" 
$result.Error -join "`r`n" 
```

Los archivos **stdout** y **stderr** de la aplicación personalizada se guardan en el contenedor **adfjobs** del servicio vinculado de Azure Storage definido al crear el servicio vinculado de Azure Batch con un GUID de la tarea. Puede obtener la ruta de acceso detallada de la salida de la ejecución de la actividad tal como se muestra en el siguiente fragmento de código: 

```shell
"exitcode": 0
"outputs": [
    "https://adfv2storage.blob.core.windows.net/adfjobs/097235ff-2c65-4d50-9770-29c029cbafbb/output/stdout.txt",
    "https://adfv2storage.blob.core.windows.net/adfjobs/097235ff-2c65-4d50-9770-29c029cbafbb/output/stderr.txt"
]
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

> [!IMPORTANT]
> - Los archivos activity.json, linkedServices.json y datasets.json se almacenan en la carpeta de tiempo de ejecución de la tarea de Batch. Para este ejemplo, activity.json, linkedServices.json y datasets.json se almacenan en la ruta de acceso https://adfv2storage.blob.core.windows.net/adfjobs/097235ff-2c65-4d50-9770-29c029cbafbb/runtime/. Debe limpiarla por separado si es necesario. 
> - Para los servicios vinculados que usan Integration Runtime autohospedado, la información confidencial (como claves o contraseñas) se cifran mediante Integration Runtime autohospedado para garantizar que las credenciales permanecen en el entorno de red privada definido por el cliente. Si el código de la aplicación personalizada hace referencia de esta forma a algunos campos confidenciales, es posible que estos no estén presentes. Si es necesario, use SecureString en extendedProperties en lugar de usar la referencia al servicio vinculado. 



## <a name="auto-scaling-of-azure-batch"></a>Escalado automático de Azure Batch
También puede crear un grupo de Lote de Azure con la característica **autoescala** . Por ejemplo, podría crear un grupo de Azure Batch con 0 VM dedicadas y una fórmula de escalado automático basada en el número de tareas pendientes. 

La fórmula del ejemplo logra el siguiente comportamiento: cuando el grupo se crea inicialmente, empieza con 1 VM. La métrica $PendingTasks define el número de tareas que están en ejecución o activas (en cola).  La fórmula busca el número promedio de tareas pendientes en los últimos 180 segundos y establece TargetDedicated en consecuencia. Garantiza que TargetDedicated nunca supera las 25 VM. Por tanto, a medida que se envían nuevas tareas, el grupo crece automáticamente y a medida que estás se completan, las VM se liberan una a una y el escalado automático las reduce. startingNumberOfVMs y maxNumberofVMs se pueden adaptar a sus necesidades.

Fórmula de escalado automático:

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Para más información, consulte [Escalado automático de los nodos de proceso en un grupo de Lote de Azure](../batch/batch-automatic-scaling.md) .

Si el grupo usa el valor predeterminado de la propiedad [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), el servicio Lote puede tardar de 15 a 30 minutos en preparar la máquina virtual antes de ejecutar la actividad personalizada.  Si el grupo usa otro valor de autoScaleEvaluationInterval diferente, el servicio Lote podría tardar el valor de autoScaleEvaluationInterval más 10 minutos.


## <a name="next-steps"></a>Pasos siguientes
Vea los siguientes artículos, en los que se explica cómo transformar datos de otras maneras: 

* [Actividad de U-SQL](transform-data-using-data-lake-analytics.md)
* [Actividad de Hive](transform-data-using-hadoop-hive.md)
* [Actividad de Pig](transform-data-using-hadoop-pig.md)
* [Actividad de MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Actividad de streaming de Hadoop](transform-data-using-hadoop-streaming.md)
* [Actividad de Spark](transform-data-using-spark.md)
* [Actividad de ejecución de Batch de Machine Learning](transform-data-using-machine-learning.md)
* [Actividad de procedimiento almacenado](transform-data-using-stored-procedure.md)
