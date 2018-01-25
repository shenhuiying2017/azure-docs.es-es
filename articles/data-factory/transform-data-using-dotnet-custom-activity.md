---
title: "Uso de actividades personalizadas en una canalización de Azure Data Factory"
description: "Obtenga información acerca de cómo crear actividades personalizadas y usarlas en una canalización de Azure Data Factory."
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
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: 2674b431ba610bccb92f6b209970af1fab110f48
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Uso de actividades personalizadas en una canalización de Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-use-custom-activities.md)
> * [Versión 2: versión preliminar](transform-data-using-dotnet-custom-activity.md)

Hay dos tipos de actividades que puede usar en una canalización de Azure Data Factory.

- [Actividades de movimiento de datos](copy-activity-overview.md) para mover datos entre [almacenes de datos de origen y receptor compatibles](copy-activity-overview.md#supported-data-stores-and-formats).
- [Actividades de transformación de datos](transform-data.md) para transformar datos mediante procesos como Azure HDInsight, Azure Batch y Azure Machine Learning. 

Para mover datos desde y hacia un almacén de datos incompatible con Data Factory, o para transformar o procesar datos de algún modo incompatible con Data Factory, puede crear una **actividad personalizada** con su propia lógica de desplazamiento o transformación de datos y usarla en una canalización. La actividad personalizada ejecuta la lógica del código personalizado en un grupo de máquinas virtuales de **Azure Batch**.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Actividad de DotNet (personalizada) en V1](v1/data-factory-use-custom-activities.md).
 

Consulte los temas siguientes si no está familiarizado con el servicio Azure Batch:

* [Aspectos básicos de Azure Batch](../batch/batch-technical-overview.md) para información general del servicio Azure Batch.
* Cmdlet [New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) para crear una cuenta de Azure Batch o [Azure Portal](../batch/batch-account-create-portal.md) para crear la cuenta de Azure Batch con Azure Portal. Consulte el tema [Uso de Azure PowerShell para administrar la cuenta de Azure Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) para obtener instrucciones detalladas sobre cómo usar este cmdlet.
* [New-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) para crear un grupo de Lote de Azure.

## <a name="azure-batch-linked-service"></a>Servicio vinculado de Azure Batch 
El siguiente JSON define un servicio vinculado de Azure Batch de ejemplo. Para obtener información detallada, vea [Compute environments supported by Azure Data Factory](compute-linked-services.md) (Entornos de proceso compatibles con Azure Data Factory).

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

| Propiedad              | DESCRIPCIÓN                              | Obligatorio |
| :-------------------- | :--------------------------------------- | :------- |
| Nombre                  | Nombre de la actividad en la canalización     | Sí      |
| Descripción           | Texto que describe para qué se usa la actividad.  | Sin        |
| Tipo                  | Para la actividad personalizada, el tipo de actividad es **Custom**. | Sí      |
| linkedServiceName     | Servicio vinculado a Azure Batch. Para obtener más información sobre este servicio vinculado, vea el artículo [Compute linked services](compute-linked-services.md) (Servicios vinculados de procesos).  | Sí      |
| command               | Comando de la aplicación personalizada que se va a ejecutar. Si la aplicación ya está disponible en el nodo del grupo de Azure Batch, se pueden omitir las propiedades resourceLinkedService y folderPath. Por ejemplo, puede especificar que el comando sea `cmd /c dir`, que el nodo del grupo de lotes de Windows admite de forma nativa. | Sí      |
| resourceLinkedService | Servicio de Azure Storage vinculado a la cuenta de almacenamiento en la que está almacenada la aplicación personalizada | Sin        |
| folderPath            | Ruta de acceso a la carpeta de la aplicación personalizada y todas sus dependencias | Sin        |
| referenceObjects      | Matriz de servicios vinculados y conjuntos de datos existentes. Los servicios vinculados y los conjuntos de datos a los que se hace referencia se pasan a la aplicación personalizada en formato JSON, por lo que el código personalizado puede hacer referencia a recursos de Data Factory | Sin        |
| extendedProperties    | Propiedades definidas por el usuario que se pueden pasar a la aplicación personalizada en formato JSON, por lo que el código personalizado puede hacer referencia a propiedades adicionales | Sin        |

## <a name="executing-commands"></a>Ejecución de comandos

Puede ejecutar directamente un comando mediante la actividad personalizada. En el ejemplo siguiente, se ejecuta un comando "echo hello world" en los nodos de grupo de destino de Azure Batch y se imprime la salida en stdout. 

  ```json
  {
    "name": "MyCustomActivity",
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
          "command": "cmd /c echo hello world"
        }
      }]
    }
  } 
  ```

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
            },
            "PropertyBagPropertyName1": "PropertyBagValue1",
            "propertyBagPropertyName2": "PropertyBagValue2",
            "dateTime1": "2015-04-12T12:13:14Z"              
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

```csharp
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

## <a name="retrieve-execution-outputs"></a>Recuperar los resultados de la ejecución

  Puede iniciar una ejecución de canalización mediante el siguiente comando de PowerShell: 

  ```.powershell
  $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
  ```
  Durante la ejecución de la canalización, puede comprobar la salida de la ejecución mediante los comandos siguientes: 

  ```.powershell
  while ($True) {
      $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

      if(!$result) {
          Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
      }
      elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
          Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
      }
      else {
          Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
          $result
          break
      }
      ($result | Format-List | Out-String)
      Start-Sleep -Seconds 15
  }

  Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
  $result.Output -join "`r`n"

  Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
  $result.Error -join "`r`n"
  ```

  Los archivos **stdout** y **stderr** de la aplicación personalizada se guardan en el contenedor **adfjobs** del servicio vinculado de Azure Storage definido al crear el servicio vinculado de Azure Batch con un GUID de la tarea. Puede obtener la ruta de acceso detallada de la salida de la ejecución de la actividad tal como se muestra en el siguiente fragmento de código: 

  ```shell
  Pipeline ' MyCustomActivity' run finished. Result:

  ResourceGroupName : resourcegroupname
  DataFactoryName   : datafactoryname
  ActivityName      : MyCustomActivity
  PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  PipelineName      : MyCustomActivity
  Input             : {command}
  Output            : {exitcode, outputs, effectiveIntegrationRuntime}
  LinkedServiceName : 
  ActivityRunStart  : 10/5/2017 3:33:06 PM
  ActivityRunEnd    : 10/5/2017 3:33:28 PM
  DurationInMs      : 21203
  Status            : Succeeded
  Error             : {errorCode, message, failureType, target}

  Activity Output section:
  "exitcode": 0
  "outputs": [
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
  ]
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
  Activity Error section:
  "errorCode": ""
  "message": ""
  "failureType": ""
  "target": "MyCustomActivity"
  ```
Si desea usar el contenido de stdout.txt en actividades de bajada, puede obtener la ruta al archivo stdout.txt en la expresión "@activity('MyCustomActivity').output.outputs[0]". 

  > [!IMPORTANT]
  > - Los archivos activity.json, linkedServices.json y datasets.json se almacenan en la carpeta de tiempo de ejecución de la tarea de Batch. Para este ejemplo, activity.json, linkedServices.json y datasets.json se almacenan en la ruta de acceso "https://adfv2storage.blob.core.windows.net/adfjobs/<GUID>/runtime/" path. Si es necesario, deberá limpiarlos por separado. 
  > - Para los servicios vinculados que usan Integration Runtime autohospedado, la información confidencial (como claves o contraseñas) se cifran mediante Integration Runtime autohospedado para garantizar que las credenciales permanecen en el entorno de red privada definido por el cliente. Si el código de la aplicación personalizada hace referencia de esta forma a algunos campos confidenciales, es posible que estos no estén presentes. Si es necesario, use SecureString en extendedProperties en lugar de usar la referencia al servicio vinculado. 

## <a name="difference-between-custom-activity-in-azure-data-factory-v2-and-custom-dotnet-activity-in-azure-data-factory-v1"></a>Diferencias entre la actividad personalizada de Azure Data Factory V2 y la actividad de DotNet (personalizada) de Azure Data Factory V1 

  En Azure Data Factory V1, puede implementar el código de la actividad de DotNet (personalizada) mediante la creación de un proyecto de biblioteca de clases .Net con una clase que implementa el método Execute de la interfaz IDotNetActivity. Los servicios vinculados, conjuntos de datos y propiedades extendidas de la carga útil de JSON de la actividad de DotNet (personalizada) se pasan al método de ejecución como objetos con tipo seguros. Para más información, consulte la actividad de [DotNet (personalizada) en V1](v1/data-factory-use-custom-activities.md). Por este motivo, el código personalizado debe escribirse en .Net Framework 4.5.2 y ejecutarse en nodos de grupo de Azure Batch basados en Windows. 

  En la actividad personalizada de Azure Data Factory V2, no tiene que implementar ninguna interfaz .Net. Ahora ya puede ejecutar directamente comandos, scripts y su propio código personalizado compilado como ejecutable. Puede hacerlo especificando la propiedad Command junto con la propiedad folderPath. La actividad personalizada carga el archivo ejecutable y las dependencias en folderpath y ejecuta el comando en su lugar. 

  El ejecutable puede acceder a los servicios vinculados, conjuntos de datos (definidos en referenceObjects) y a las propiedades extendidas definidas en la carga útil de JSON de la actividad personalizada como archivos JSON. Puede acceder a las propiedades requeridas mediante el serializador JSON como se muestra en el ejemplo de código anterior SampleApp.exe. 

  Con los cambios introducidos en la actividad personalizada de Azure Data Factory V2, puede decidir si desea escribir su lógica de código personalizado en el lenguaje que desee y ejecutarlo en sistemas operativos Windows o Linux que sean compatibles con Azure Batch. 

  En la tabla siguiente se describen las diferencias entre la actividad personalizada de Data Factory V2 y la actividad de DotNet de Data Factory V1 (personalizada): 


|Diferencias      |Actividad personalizada de ADFv2      |Actividad de DotNet de ADFv1 (personalizada)      |
| ---- | ---- | ---- |
|Formas de definir la lógica personalizada      |Ejecutar cualquier archivo ejecutable (existente o uno propio que implemente)      |Implementar un archivo DLL de .Net      |
|Entorno de ejecución de la lógica personalizada      |Windows o Linux      |Windows (.Net Framework 4.5.2)      |
|Ejecución de scripts      |Admite la ejecución directa de scripts (por ejemplo "cmd /c echo hello world" en la VM de Windows)      |Requiere la implementación en el archivo DLL de .Net      |
|Conjunto de datos requerido      |Opcional      |Necesario para encadenar actividades y pasar información      |
|Pasar información de actividad a la lógica personalizada      |Mediante ReferenceObjects (LinkedServices y conjuntos de datos) y ExtendedProperties (propiedades personalizadas) y      |Mediante ExtendedProperties (propiedades personalizadas), y conjuntos de datos de entrada y salida      |
|Recuperación de información en lógica personalizada      |Analizar los archivos activity.json, linkedServices.json y datasets.json almacenados en la misma carpeta que el ejecutable      |Mediante SDK de .Net (.Net Frame 4.5.2)      |
|Registro      |Escribe directamente en STDOUT      |Se implementa el registrador en el archivo DLL de .Net      |


  Si tiene código .Net ya existente escrito para la actividad de DotNet (personalizada) de V1, deberá modificar el código para que funcione con la actividad personalizada de V2 con las siguientes directrices de alto nivel:  

   - Cambie el proyecto de una biblioteca de clases .Net a una aplicación de consola. 
   - Inicie la aplicación con el método Main. Ya no es necesario el método Execute de la interfaz IDotNetActivity. 
   - Lea y analice los servicios vinculados, los conjuntos de datos y la actividad con el serializador JSON en lugar de con objetos con tipo seguros y pase los valores de las propiedades requeridas a la lógica del código personalizado principal. Consulte el código anterior de SampleApp.exe como ejemplo. 
   - Ya no se admite el objeto de registrador y las salidas ejecutables se pueden imprimir en la consola y se guardan en stdout.txt. 
   - El paquete NuGet Microsoft.Azure.Management.DataFactories ya no es necesario. 
   - Compile el código, cargue el ejecutable y las dependencias en Azure Storage y defina la ruta de acceso en la propiedad folderPath. 

Para obtener un ejemplo completo de cómo el archivo DLL completo y la canalización de ejemplo que se describen en el documento de Data Factory V1 [Uso de actividades personalizadas en una canalización de Azure Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) se pueden volver a escribir en el estilo de la actividad personalizada de Data Factory V2. Consulte el [ejemplo de actividad personalizada de Data Factory V2](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFv2CustomActivitySample). 

## <a name="auto-scaling-of-azure-batch"></a>Escalado automático de Azure Batch
También puede crear un grupo de Azure Batch con la característica **autoescala** . Por ejemplo, podría crear un grupo de Azure Batch con 0 VM dedicadas y una fórmula de escalado automático basada en el número de tareas pendientes. 

La fórmula del ejemplo logra el siguiente comportamiento: cuando el grupo se crea inicialmente, empieza con 1 VM. La métrica $PendingTasks define el número de tareas que están en ejecución o activas (en cola).  La fórmula busca el número promedio de tareas pendientes en los últimos 180 segundos y establece TargetDedicated en consecuencia. Garantiza que TargetDedicated nunca supera las 25 VM. Por tanto, a medida que se envían nuevas tareas, el grupo crece automáticamente y a medida que estás se completan, las VM se liberan una a una y el escalado automático las reduce. startingNumberOfVMs y maxNumberofVMs se pueden adaptar a sus necesidades.

Fórmula de escalado automático:

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Para más información, consulte [Escalado automático de los nodos de proceso en un grupo de Azure Batch](../batch/batch-automatic-scaling.md) .

Si el grupo usa el valor predeterminado de la propiedad [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), el servicio Batch puede tardar de 15 a 30 minutos en preparar la máquina virtual antes de ejecutar la actividad personalizada.  Si el grupo usa otro valor de autoScaleEvaluationInterval diferente, el servicio Batch podría tardar el valor de autoScaleEvaluationInterval más 10 minutos.


## <a name="next-steps"></a>pasos siguientes
Vea los siguientes artículos, en los que se explica cómo transformar datos de otras maneras: 

* [Actividad de U-SQL](transform-data-using-data-lake-analytics.md)
* [Actividad de Hive](transform-data-using-hadoop-hive.md)
* [Actividad de Pig](transform-data-using-hadoop-pig.md)
* [Actividad de MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Actividad de streaming de Hadoop](transform-data-using-hadoop-streaming.md)
* [Actividad de Spark](transform-data-using-spark.md)
* [Actividad de ejecución de Batch de Machine Learning](transform-data-using-machine-learning.md)
* [Actividad de procedimiento almacenado](transform-data-using-stored-procedure.md)
