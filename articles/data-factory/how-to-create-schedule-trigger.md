---
title: "Creación de desencadenadores de programación en Azure Data Factory | Microsoft Docs"
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
ms.openlocfilehash: 6b92e8402d372e29e264dc70b128124973d66bb9
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Creación de un desencadenador que ejecuta una canalización en una programación
En este artículo se proporciona información sobre el desencadenador de programación y los pasos para crear, iniciar y supervisar un desencadenador. Para otros tipos de desencadenadores, consulte [Ejecución y desencadenadores de canalización](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Introducción a la versión 1 de Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

### <a name="schedule-trigger-json-definition"></a>Definición JSON del desencadenador de programación
Cuando se crea un desencadenador de programación, puede especificar la programación y periodicidad mediante JSON, como se muestra en el ejemplo de esta sección.

Para hacer que el desencadenador de programación dé inicio a una ejecución de canalización, incluya una referencia de canalización de la canalización en particular en la definición del desencadenador. Las canalizaciones y los desencadenadores tienen una relación de varios a varios. Varios desencadenadores pueden comenzar una única canalización. Un único desencadenador puede iniciar varias canalizaciones.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // optional, how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": : [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ]
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  La propiedad **parameters** es una propiedad obligatoria dentro de las **canalizaciones**. Aunque la canalización no tome ningún parámetro, incluya una propiedad JSON vacía para los parámetros, ya que esta debe existir.


### <a name="overview-schedule-trigger-schema"></a>Información general: esquema del desencadenador de programación
En la tabla siguiente se muestra una descripción general de los elementos más importantes relacionados con la periodicidad y la programación de un desencadenador:

Propiedad JSON |     DESCRIPCIÓN
------------- | -------------
startTime | startTime es una fecha y hora. Para las programaciones sencillas, startTime es la primera aparición. Para las programaciones complejas, el desencadenador no se inicia antes de startTime.
endTime | Especifica la fecha y hora de finalización para el desencadenador. El desencadenador no se ejecuta después de esta fecha. No es válido tener un valor de endTime en el pasado. Se trata de una propiedad opcional.
timeZone | Actualmente, solo se admite UTC.
recurrence | El objeto recurrence especifica las reglas de periodicidad para el desencadenador. El objeto recurrence admite los elementos siguientes: frequency, interval, endTime, count y schedule. Si se define recurrence, se requiere frequency; los otros elementos de recurrence son opcionales.
frequency | Representa la unidad de frecuencia con la que se repite el desencadenador. Los valores admitidos son: `minute`, `hour`, `day`, `week` o `month`.
interval | El intervalo es un entero positivo. Indica el intervalo de la frecuencia que determina cuán seguido se ejecuta el desencadenador. Por ejemplo, si interval es 3 y frequency es “week”, el desencadenador se repite cada tres semanas.
schedule | Un desencadenador con una frecuencia especificada modifica su periodicidad según una programación periódica. La propiedad schedule contiene modificaciones basadas en minutos, horas, días de la semana, días del mes y número de semana.


### <a name="overview-schedule-trigger-schema-defaults-limits-and-examples"></a>Información general: valores predeterminados del esquema de desencadenador de programación, límites y ejemplos

Nombre JSON | Tipo de valor | ¿Necesario? | Valor predeterminado | Valores válidos | Ejemplo
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | string | Sí | None | Fechas-horas ISO-8601 | ```"startTime" : "2013-01-09T09:30:00-08:00"```
recurrence | Objeto | Sí | None | Objeto de periodicidad | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
interval | Number | Sin  | 1 | 1 a 1000. | ```"interval":10```
endTime | string | Sí | None | Valor de fecha y hora que representa un periodo de tiempo en el futuro | `"endTime" : "2013-02-09T09:30:00-08:00"`
schedule | Objeto | Sin  | None | Objeto de programación | `"schedule" : { "minute" : [30], "hour" : [8,17] }`

### <a name="deep-dive-starttime"></a>Profundización: startTime
La siguiente tabla captura cómo startTime controla el modo en que se ejecuta un desencadenador:

Valor de startTime | Periodicidad sin programación | Periodicidad con programación
--------------- | --------------------------- | ------------------------
Hora de inicio en el pasado | Calcula la primera hora de ejecución futura después de la hora de inicio y se ejecuta a esa hora.<p>Realiza ejecuciones posteriores según el cálculo desde la última hora de ejecución.</p><p>Consulte el ejemplo que sigue a esta tabla.</p> | El desencadenador se inicia _no antes que_ la hora de inicio especificada. La primera repetición se basa en la programación que se calcula a partir de la hora de inicio. <p>Realizar las sucesivas ejecuciones según la programación de periodicidad</p>
Hora de inicio en el futuro o en el presente | Se ejecuta una vez a la hora de inicio especificada. <p>Realiza las ejecuciones posteriores según el cálculo desde la última hora de ejecución.</p> | El desencadenador se inicia _no antes que_ la hora de inicio especificada. La primera repetición se basa en la programación que se calcula a partir de la hora de inicio.<p>Realiza las sucesivas ejecuciones según la programación de periodicidad.</p>

Veamos un ejemplo de lo que sucede cuando startTime se encuentra en el pasado, con recurrence, pero sin schedule. Suponga que la fecha actual es `2017-04-08 13:00`, startTime es `2017-04-07 14:00`, y recurrence es de dos días (definida con frequency: day e interval: 2). Tenga en cuenta que startTime se encuentra en el pasado y se produce antes de la hora actual.

En estas condiciones, la primera ejecución será `2017-04-09 at 14:00`. El motor de Scheduler calcula las repeticiones de la ejecución desde la hora de inicio. Se descartan las instancias en el pasado. El motor utiliza la instancia siguiente que tiene lugar en el futuro. En este caso, startTime es `2017-04-07 at 2:00pm`, así que la siguiente instancia es dos días a partir de ese momento, que es `2017-04-09 at 2:00pm`.

La fecha de la primera ejecución es la misma incluso si startTime es `2017-04-05 14:00` o `2017-04-01 14:00`. Después de la primera ejecución, las ejecuciones posteriores se calculan con la programación. Por lo tanto, son `2017-04-11 at 2:00pm`, luego `2017-04-13 at 2:00pm`, luego, `2017-04-15 at 2:00pm`, etc.

Por último, cuando un desencadenador tiene una programación, si no se establecen en la programación las horas y minutos, se asume el valor predeterminado de las horas y minutos de la primera ejecución, respectivamente.

### <a name="deep-dive-schedule"></a>Profundización: schedule
Por un lado, schedule puede limitar el número de ejecuciones de desencadenadores. Por ejemplo, si un desencadenador con frequency de “month” tiene un valor de schedule que se ejecuta solo el día 31, el desencadenador se ejecuta solo en los meses que tienen 31 días.

En cambio, una programación también puede ampliar el número de ejecuciones de desencadenadores. Por ejemplo, si un desencadenador con frequency de “month” tiene un valor de schedule que se ejecuta los días 1 y 2 del mes, el desencadenador se ejecuta en los días 1 y 2 del mes en lugar de una vez al mes.

Si se especifican varios elementos de programación, el orden de evaluación es de mayor a menor: número de semana, día del mes, día de la semana, hora y minuto.

En la siguiente tabla se describen los elementos de schedule con detalle:


Nombre JSON | DESCRIPCIÓN | Valores válidos
--------- | ----------- | ------------
minutes | Minutos de la hora en la que se ejecuta el desencadenador. | <ul><li>Entero</li><li>Matriz de enteros</li></ul>
hours | Horas del día en la que se ejecuta el desencadenador. | <ul><li>Entero</li><li>Matriz de enteros</li></ul>
weekDays | Días de la semana en los que se ejecuta el desencadenador. Solo se puede especificar con una frecuencia semanal. | <ul><li>Lunes, martes, miércoles, jueves, viernes, sábado o domingo</li><li>Matriz de cualquiera de los valores anteriores (tamaño máximo de la matriz 7)</li></p>No distingue mayúsculas de minúsculas</p>
monthlyOccurrences | Determina los días del mes en los que se ejecutar el desencadenador. Solo se puede especificar con una frecuencia mensual. | Matriz de objetos de monthlyOccurrence: `{ "day": day,  "occurrence": occurence }`. <p> day es el día de la semana en el que se ejecutar el desencadenador; por ejemplo, `{Sunday}` es cada domingo del mes. Necesario.<p>El valor de occurrence es la repetición del día durante el mes, por ejemplo, `{Sunday, -1}` es el último domingo del mes. Opcional.
monthDays | Día del mes en el que se ejecutar el desencadenador. Solo se puede especificar con una frecuencia mensual. | <ul><li>Cualquier valor <= -1 y >= -31</li><li>Cualquier valor >= 1 y <= 31</li><li>Una matriz de valores</li>


## <a name="examples-recurrence-schedules"></a>Ejemplos: programaciones de periodicidad
En esta sección se muestran ejemplos de programaciones de periodicidad, centrándose en el objeto schedule y sus subelementos.

Todas las programaciones siguientes asumen que interval está establecido en 1. Además, suponga la frecuencia correcta de acuerdo con lo que está en schedule: por ejemplo, no puede usar la frecuencia “day” y tener una modificación “monthDays” en la programación. Estas restricciones se mencionan en la tabla de la sección anterior.

Ejemplo | DESCRIPCIÓN
------- | -----------
`{"hours":[5]}` | Se ejecuta a las 5 a. m. todos los días.
`{"minutes":[15], "hours":[5]}` | Se ejecuta a las 5:15 a. m. todos los días.
`{"minutes":[15], "hours":[5,17]}` | Se ejecuta a las 5:15 a. m. y 5:15 p. m. todos los días.
`{"minutes":[15,45], "hours":[5,17]}` | Se ejecuta a las 5:15 a. m., 5:45 a. m., 5:15 p. m. y 5:45 p. m. todos los días.
`{"minutes":[0,15,30,45]}` | Se ejecuta cada 15 minutos.
`{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Se ejecuta cada hora. Este desencadenador se ejecuta cada hora. Los minutos se controlan mediante startTime, si se especifica una fecha y hora de inicio, o si no se especifica, en el momento de creación. Por ejemplo, si la hora de inicio o la hora de creación (lo que corresponda) es 12:25 p. m., el desencadenador se ejecuta a las 00:25, 01:25, 02:25… 23:25. La programación equivale a tener un desencadenador con un valor de frequency de “hour”, un valor de interval de 1 y ningún valor de schedule. La diferencia es que esta programación puede usarse con diferentes valores en frequency e interval para crear también otros desencadenadores. Por ejemplo, si el valor de frequency fuera “month”, la programación se ejecutaría solo una vez al mes en lugar de todos los días si frequency fuera “day”.
`{"minutes":[0]}` | Se ejecuta cada hora en punto. Este desencadenador también se ejecuta cada hora, pero a la hora exacta (por ejemplo, 12 a. m., 1 a. m., 2 a. m., etc.). Esta configuración equivale a un desencadenador con frequency de “hour”, startTime con cero minutos y sin schedule si frequency fuera “day”; pero si frequency fuera “week” o “month”, la programación podría ejecutarse solo un día a la semana o un día del mes, respectivamente.
`{"minutes":[15]}` | Se ejecuta 15 minutos después de cada hora en punto. Se ejecuta cada hora, comenzando en 00:15 a.m., 1:15 a.m., 2:15 a.m., etc. y terminando en 10:15 p.m. y las 11:15 p.m.
`{"hours":[17], "weekDays":["saturday"]}` | Se ejecuta a las 5 p. m. los sábados cada semana.
`{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Se ejecuta a las 5 p. m. el lunes, el miércoles y el viernes de cada semana.
`{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Se ejecuta a las 5:15 p. m. y las 5:45 p. m. el lunes, el miércoles y el viernes cada semana.
`{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Se ejecuta cada 15 minutos los días laborables.
`{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Se ejecuta cada 15 minutos los días laborables entre las 9 a. m. y las 4:45 p. m.
`{"weekDays":["tuesday", "thursday"]}` | Se ejecuta los martes y los jueves a la hora de inicio especificada.
`{"minutes":[0], "hours":[6], "monthDays":[28]}` | Se ejecuta a las 6 a. m. del día 28 de cada mes (suponiendo una frecuencia mensual).
`{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Se ejecuta a las 6 a. m. el último día del mes. Si desea ejecutar un desencadenador en el último día del mes, use -1 en lugar del día 28, 29, 30 o 31.
`{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Se ejecuta a las 6 a. m. el primer y último día de cada mes.
`{monthDays":[1,14]}` | Se ejecuta el primer y decimocuarto día de cada mes a la hora de inicio especificada.
`{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Se ejecuta el primer viernes de cada mes a las 5 a. m.
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Se ejecuta el primer viernes de cada mes a la hora de inicio especificada.
`{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Se ejecuta el tercer viernes desde el final del mes, cada mes, a la hora de inicio.
`{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Se ejecuta el primer y el último viernes de cada mes a las 5:15 a. m.
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Se ejecuta el primer y el último viernes de cada mes a la hora de inicio especificada.
`{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Se ejecuta el quinto viernes de cada mes a la hora de inicio. Si no hay ningún quinto viernes en un mes, la canalización no se ejecuta, ya que se ha programado para ejecutarse solo el quinto viernes.  Puede considerar usar -1 en lugar de 5 para la repetición si desea ejecutar el desencadenador en el último viernes del mes.
`{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Se ejecuta cada 15 minutos el último viernes del mes.
`{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Se ejecuta a las 5:15 a. m., 5:45 a. m., 5:15 p. m. y 5:45 p. m. el tercer miércoles de cada mes.


## <a name="use-azure-powershell"></a>Uso de Azure PowerShell
En esta sección se muestra cómo usar Azure PowerShell para crear, iniciar y supervisar un desencadenador de programador. Si quiere ver este ejemplo en funcionamiento, primero debe seguir la [Guía de inicio rápido: Creación de una factoría de datos con Azure PowerShell](quickstart-create-data-factory-powershell.md). A continuación, agregue el código siguiente al método principal, de modo que se crea e inicia un desencadenador de programación que se ejecuta cada 15 minutos. El desencadenador está asociado a una canalización (**Adfv2QuickStartPipeline**) que se crea como parte de la guía de inicio rápido.

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

## <a name="next-steps"></a>pasos siguientes
Para obtener información detallada acerca de los desencadenadores, consulte el artículo [Pipeline execution and triggers](concepts-pipeline-execution-triggers.md#triggers) (Ejecución de canalizaciones y desencadenadores).
