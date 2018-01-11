---
title: "Creación de desencadenadores en Azure Data Factory | Microsoft Docs"
description: "Obtenga información acerca de cómo crear un desencadenador en Azure Data Factory para que ejecute una canalización en una programación."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: shlo
ms.openlocfilehash: eed286c01604ab0e9ac2113d56cbce268503668d
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Creación de un desencadenador que ejecuta una canalización en una programación
En este artículo se explica cómo crear, iniciar y supervisar un desencadenador. Para obtener información conceptual acerca de los desencadenadores, consulte el artículo [Pipeline execution and triggers](concepts-pipeline-execution-triggers.md) (Ejecución de canalizaciones y desencadenadores).

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Introducción a la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="use-azure-powershell"></a>Uso de Azure PowerShell
En esta sección se muestra cómo usar Azure PowerShell para crear, iniciar y supervisar un desencadenador. Si quiere ver este ejemplo en funcionamiento, primero debe seguir la [Guía de inicio rápido: Creación de una factoría de datos con Azure PowerShell](quickstart-create-data-factory-powershell.md). A continuación, agregue el código siguiente al método principal, de modo que se crea e inicia un desencadenador de programación que se ejecuta cada 15 minutos. El desencadenador está asociado a una canalización (**Adfv2QuickStartPipeline**) que se crea como parte de la guía de inicio rápido.

1. Cree un archivo JSON llamado MyTrigger.json en la carpeta C:\ADFv2QuickStartPSH con el siguiente contenido: 

    > [!IMPORTANT]
    > Establezca **startTime** en la hora UTC actual y **endTime** en una hora posterior a la hora UTC actual antes de guardar el archivo JSON.

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```
    
    En el fragmento de código de JSON: 
    - El elemento **type** del desencadenador se establece en **ScheduleTrigger**. 
    - El elemento **frequency** se establece en **Minute** e **interval** se establece en **15**. Por lo tanto, el desencadenador ejecuta la canalización cada 15 minutos entre las horas de inicio y finalización. 
    - El valor de **endTime** es una hora después del valor de **startTime**, por lo que el desencadenador ejecuta la canalización después de 15 minutos, 30 minutos y 45 minutos de la hora de inicio. No se olvide de actualizar el valor de startTime a la hora UTC actual y endTime a una hora posterior que startTime.  
    - El desencadenador está asociado con la canalización **Adfv2QuickStartPipeline**. Para asociar varias canalizaciones con un desencadenador, agregue más secciones **pipelineReference**. 
    - La canalización de la guía de inicio rápido toma dos **parámetros**. Por lo tanto, debe pasar valores para esos parámetros desde el desencadenador. 
2. Inicie un desencadenador mediante el cmdlet **Set-AzureRmDataFactoryV2Trigger**.

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
3. Confirme que el estado del desencadenador es **Detenido** mediante el uso del cmdlet **Get-AzureRmDataFactoryV2Trigger**. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" 
    ```
4. Inicie el desencadenador mediante el cmdlet **Start-AzureRmDataFactoryV2Trigger**: 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" 
    ```
5. Confirme que el estado del desencadenador es **Iniciado** mediante el uso del cmdlet **Get-AzureRmDataFactoryV2Trigger**.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" 
    ```
6.  Obtenga ejecuciones de disparador con PowerShell mediante el cmdlet **Get-AzureRmDataFactoryV2TriggerRun**. Para obtener la información sobre las ejecuciones de desencadenador, ejecute el siguiente comando periódicamente: actualice los valores de **TriggerRunStartedAfter** y **TriggerRunStartedBefore** para que coincidan con los valores de la definición del desencadenador. 

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```

    Para supervisar las ejecuciones del desencadenador o la canalización en Azure Portal, consulte la sección sobre la [supervisión de ejecuciones de canalización](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="use-net-sdk"></a>Uso del SDK de .NET
En esta sección se muestra cómo usar el SDK de.NET para crear, iniciar y supervisar un desencadenador. Si quiere ver este código en funcionamiento, primero debe seguir la [guía de inicio rápido sobre la creación de una factoría de datos con SDK de .NET](quickstart-create-data-factory-dot-net.md). A continuación, agregue el código siguiente al método principal, de modo que se crea e inicia un desencadenador de programación que se ejecuta cada 15 minutos. El desencadenador está asociado a una canalización (**Adfv2QuickStartPipeline**) que se crea como parte de la guía de inicio rápido. 

```csharp
            //create the trigger
            Console.WriteLine("Creating the trigger");

            // set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // set the start time to current UTC time and the end time to be one hour after.
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // now, create the trigger by invoking CreateOrUpdate method. 
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            //start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);

```

Para supervisar una ejecución de desencadenador, agregue el código siguiente antes de la última instrucción `Console.WriteLine`: 

```csharp
            // Check the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Para supervisar las ejecuciones del desencadenador o la canalización en Azure Portal, consulte la sección sobre la [supervisión de ejecuciones de canalización](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="use-python-sdk"></a>Uso del SDK de Python
En esta sección se muestra cómo usar el SDK de Python para crear, iniciar y supervisar un desencadenador. Si quiere ver este código en funcionamiento, primero debe seguir la [guía de inicio rápido sobre la creación de una factoría de datos con el SDK de Python](quickstart-create-data-factory-python.md). A continuación, agregue el siguiente bloque de código después del bloque de código para "supervisar la ejecución de canalización" en el script de Python. Este código crea un desencadenador de programación que se ejecuta cada 15 minutos entre las horas de inicio y finalización especificadas. Actualice el valor de start_time a la hora UTC actual y end_time a una hora posterior a la hora UTC actual. 

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC') 
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```
Para supervisar las ejecuciones del desencadenador o la canalización en Azure Portal, consulte la sección sobre la [supervisión de ejecuciones de canalización](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="use-resource-manager-template"></a>Uso de plantillas de Resource Manager
Puede usar una plantilla de Azure Resource Manager para crear un desencadenador. Para obtener instrucciones detalladas, consulte [Creación de una instancia de Azure Data Factory mediante una plantilla de Resource Manager](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Transmisión de la hora de inicio del desencadenador a una canalización
En la versión 1, Azure Data Factory admitía la lectura y la escritura de datos con particiones por medio de las variables del sistema SliceStart, SliceEnd, WindowStart y WindowEnd. En la versión 2, puede lograr este comportamiento mediante un parámetro de canalización y la hora de inicio o programada del desencadenador como un valor del parámetro. En el siguiente ejemplo, la hora programada del desencadenador se pasa como un valor del parámetro de canalización scheduledRunTime. 

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    
Para obtener más información, consulte [How to read and write large data files](how-to-read-write-partitioned-data.md) (Cómo leer o escribir datos con particiones).

## <a name="next-steps"></a>Pasos siguientes
Para obtener información detallada acerca de los desencadenadores, consulte el artículo [Pipeline execution and triggers](concepts-pipeline-execution-triggers.md#triggers) (Ejecución de canalizaciones y desencadenadores).