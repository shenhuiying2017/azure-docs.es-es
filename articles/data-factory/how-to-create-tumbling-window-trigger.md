---
title: "Creación de desencadenadores de ventana de saltos de tamaño constante en Azure Data Factory | Microsoft Docs"
description: "Obtenga información acerca de cómo crear un desencadenador en Azure Data Factory para que ejecute una canalización en una ventana de saltos de tamaño constante."
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
ms.date: 01/05/2018
ms.author: shlo
ms.openlocfilehash: a3b056ae4bb4eda26fec58ca3b6bed7f0744e36e
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Creación de un desencadenador que ejecuta una canalización en una ventana de saltos de tamaño constante
En este artículo se explica cómo crear, iniciar y supervisar un desencadenador de ventana de saltos de tamaño constante. Para obtener información conceptual acerca de los desencadenadores y los tipos compatibles, consulte, [Ejecución y desencadenadores de canalización](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Introducción a la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Los desencadenadores de ventana de saltos de tamaño constante son un tipo de desencadenador que se activa en un intervalo de tiempo periódico a partir de una hora de inicio especificada, mientras conserva el estado. Las ventanas de saltos de tamaño constante son una serie de intervalos de tiempo de tamaño fijo, contiguos y que no se superponen. Un desencadenador de ventana de saltos de tamaño constante tiene una relación de 1:1 con una canalización y solo puede hacer referencia a una única canalización.

## <a name="tumbling-window-trigger-type-properties"></a>Propiedades del tipo de desencadenador de ventana de saltos de tamaño constante
```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": "<<Minute/Hour>>",
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: "<<datetime – optional>>"",
            "delay": "<<timespan – optional>>",
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count":  <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
        },
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "parameter1": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter2": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
                }
            }
      }    
}
```  

En la tabla siguiente se muestra una descripción general de los elementos más importantes relacionados con la periodicidad y la programación de un desencadenador de ventana de saltos de tamaño constante.

| **Nombre JSON** | **Descripción** | **Valores permitidos** | **Obligatorio** |
|:--- |:--- |:--- |:--- |
| **type** | Tipo de desencadenador. Viene fijado como "TumblingWindowTrigger." | string | Sí |
| **runtimeState** | <readOnly> Valores posibles: Started, Stopped, Disabled | string | Sí, readOnly |
| **frequency** |La cadena *frequency* representa la unidad de frecuencia en la que se repite el desencadenador. Los valores admitidos son "minute" y "hour". Si la hora de inicio tiene partes de fecha más detalladas que la frecuencia, se tendrán en cuenta para calcular los límites de ventana. Por ejemplo: si la frecuencia es cada hora y la hora de inicio es 2016-04-01T10:10:10Z, la primera ventana es (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z.)  | String. Los tipos admitidos son "minute", "hour". | Sí |
| **interval** |*interval* es un entero positivo e indica el intervalo para el valor de *frequency* que determina la frecuencia con la que se ejecutará el desencadenador. Por ejemplo, si *interval* es 3 y *frequency es* "hour", el desencadenador se repite cada tres horas. | Entero | Sí |
| **startTime**|*startTime* es una fecha y hora. *startTime* es la primera aparición y puede estar en el pasado. El primer intervalo de desencadenador será (startTime, startTime + interval). | Datetime | Sí |
| **endTime**|*endTime* es una fecha y hora. *endTime* es la última aparición y puede estar en el pasado. | Datetime | Sí |
| **delay** | Especifica el retraso antes de iniciar el procesamiento de los datos de la ventana. La ejecución de la canalización se inicia después del tiempo de ejecución esperado + el retraso. El retraso define el tiempo de espera del desencadenador antes de activar la nueva ejecución. No altera la hora de inicio de la ventana. | Intervalo de tiempo (ejemplo: 00:10:00 implica un retraso de 10 minutos) |  Nº El valor predeterminado es "00:00:00" |
| **max concurrency** | Número de ejecuciones simultáneas del desencadenador que se activan para las ventanas que están listas. Ejemplo: si estamos intentando una reposición para cada hora de ayer, serían 24 ventanas. Si simultaneidad = 10, los eventos del desencadenador se activan solo para las 10 primeras ventanas (00:00-01:00 - 09:00-10:00). Una vez completadas las 10 primeras ejecuciones de canalización desencadenadas, se activan las ejecuciones de desencadenador para los 10 siguientes (10:00-11:00 - 19:00-20:00). Siguiendo con el ejemplo de simultaneidad = 10, si hay 10 ventanas listas, habrá también 10 ejecuciones de canalización. Si solo hay 1 ventana lista, solo habrá 1 de ejecución de canalización. | Entero | Sí. Valores posibles: 1-50 |
| **retryPolicy: Count** | El número de reintentos antes de que la ejecución de la canalización se marque como error.  | Entero |  Nº El valor predeterminado es 0 reintentos. |
| **retryPolicy: intervalInSeconds** | El retraso entre reintentos, en segundos. | Entero |  Nº El valor predeterminado es 30 segundos. |

### <a name="using-system-variables-windowstart-and-windowend"></a>Uso de variables del sistema: WindowStart y WindowEnd

Si desea utilizar las variables WindowStart y WindowEnd del desencadenador de la ventana de saltos de tamaño constante en la definición de la **canalización** (es decir, para parte de una consulta), debe pasar las variables como parámetros a la canalización en la definición del **desencadenador**, como:
```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "MyWindowStart": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "MyWindowEnd": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    }
                }
            }
      }    
}
```  

A continuación, en la definición de la canalización, para usar los valores WindowStart y WindowEnd, use los parámetros correspondientes de "MyWindowStart" y "MyWindowEnd"

### <a name="notes-on-backfill"></a>Notas sobre la reposición
Cuando hay varias ventanas abiertas para su ejecución (especialmente en el escenario de reposición), el orden de ejecución de las ventanas es determinante, y será del intervalo más antiguo al más nuevo. No hay ninguna manera de cambiar este comportamiento en este momento.

### <a name="updating-an-existing-triggerresource"></a>Actualización de un valor TriggerResource existente
* Si se cambia la frecuencia (o el tamaño de la ventana) del desencadenador, el estado de la ventana ya procesada *no* se restablecerá. El desencadenador seguirá activando las ventanas a partir de la última que ejecutó utilizando el nuevo tamaño de ventana.
* Si se cambia la hora de finalización del desencadenador (se agrega o se actualiza), el estado de las ventanas ya procesadas *no* se restablecerá. El desencadenador simplemente respetará la nueva hora de finalización. Si la hora de finalización es anterior a las ventanas ya ejecutadas, el desencadenador se detendrá. En caso contrario, se detendrá cuando llegue la nueva hora de finalización.

## <a name="sample-using-azure-powershell"></a>Ejemplo mediante Azure PowerShell
En esta sección se muestra cómo usar Azure PowerShell para crear, iniciar y supervisar un desencadenador.

1. Cree un archivo JSON llamado MyTrigger.json en la carpeta C:\ADFv2QuickStartPSH con el siguiente contenido:

   > [!IMPORTANT]
   > Establezca **startTime** en la hora UTC actual y **endTime** en una hora posterior a la hora UTC actual antes de guardar el archivo JSON.

    ```json   
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```  
2. Inicie un desencadenador mediante el cmdlet **Set-AzureRmDataFactoryV2Trigger**.

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    
3. Confirm that the status of the trigger is **Stopped** by using the **Get-AzureRmDataFactoryV2Trigger** cmdlet.

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

## <a name="next-steps"></a>pasos siguientes
Para obtener información detallada acerca de los desencadenadores, consulte el artículo [Pipeline execution and triggers](concepts-pipeline-execution-triggers.md#triggers) (Ejecución de canalizaciones y desencadenadores).
