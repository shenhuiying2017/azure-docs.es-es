---
title: "Supervisión mediante programación de una factoría de datos de Azure | Microsoft Docs"
description: "Aprenda a supervisar una canalización en una factoría de datos mediante distintos kits de desarrollo de software (SDK)."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: spelluru
ms.openlocfilehash: 8d47ebe5ad40aee8289420e8424ec4d745baf2df
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Supervisión mediante programación de una factoría de datos de Azure
En este artículo se describe cómo supervisar una canalización de una factoría de datos mediante distintos kits de desarrollo de software (SDK). 

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Supervisión y administración de canalizaciones en Data Factory, versión 1](v1/data-factory-monitor-manage-pipelines.md).

## <a name="net"></a>.NET
Para ver un tutorial completo sobre cómo crear y supervisar una canalización mediante el SDK. de NET, consulte [Creación de una factoría de datos y una canalización con SDK de .NET](quickstart-create-data-factory-dot-net.md).

1. Agregue el código siguiente para comprobar continuamente el estado de la ejecución de canalización hasta que termine de copiar los datos.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Agregue el código siguiente que recupera detalles de la ejecución de actividad de copia, como el tamaño de los datos leídos o escritos.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

Para obtener la documentación completa del SDK de .NET. consulte la [referencia del SDK de .NET de Data Factory](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet).

## <a name="python"></a>Python
Para ver un tutorial completo sobre cómo crear y supervisar una canalización mediante el SDK. de Python, consulte [Creación de una factoría de datos y una canalización con Python](quickstart-create-data-factory-python.md).

Para supervisar la ejecución de canalización, agregue el código siguiente:

```python
#Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Para obtener la documentación completa del SDK de .NET. consulte la [referencia del SDK de Python de Data Factory](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>API DE REST
Para ver un tutorial completo sobre cómo crear y supervisar una canalización mediante la API de REST, consulte [Creación de una instancia de Azure Data Factory y una canalización mediante la API de REST](quickstart-create-data-factory-rest-api.md).
 
1. Ejecute el script siguiente para comprobar continuamente el estado de ejecución de la canalización hasta que termine de copiar los datos.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. Ejecute el script siguiente para recuperar detalles de la ejecución de la actividad de copia, como el tamaño de los datos leídos o escritos.

    ```PowerShell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

Para obtener la documentación completa sobre la API de REST, consulte la [referencia de la API de REST de Data Factory](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
Para ver un tutorial completo sobre cómo crear y supervisar una canalización mediante PowerShell, consulte [Creación de una factoría de datos y una canalización con PowerShell](quickstart-create-data-factory-powershell.md).

1. Ejecute el script siguiente para comprobar continuamente el estado de ejecución de la canalización hasta que termine de copiar los datos.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```
2. Ejecute el script siguiente para recuperar detalles de la ejecución de la actividad de copia, como el tamaño de los datos leídos o escritos.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

Para obtener la documentación completa sobre los cmdlets de PowerShell, consulte la [referencia de los cmdlets de PowerShell de Data Factory](/powershell/module/azurerm.datafactoryv2/?view=azurermps-4.4.1).

## <a name="next-steps"></a>pasos siguientes
Consulte el artículo sobre la [supervisión de canalizaciones mediante Azure Monitor](monitor-using-azure-monitor.md) para obtener información sobre cómo usar Azure Monitor para supervisar las canalizaciones de factoría de datos. 

