---
title: "Ejecución y desencadenadores de canalización en Azure Data Factory | Microsoft Docs"
description: "En este artículo se proporciona información sobre cómo ejecutar una canalización de Azure Data Factory a petición o mediante la creación de un desencadenador."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: c472cf080f8138ec6d0210f3ca4a8b3f3c33e7ae
ms.sourcegitcommit: 4ac89872f4c86c612a71eb7ec30b755e7df89722
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2017
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Ejecución y desencadenadores de canalización en Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: disponibilidad general](v1/data-factory-scheduling-and-execution.md)
> * [Versión 2: versión preliminar](concepts-pipeline-execution-triggers.md)

Una **ejecución de canalización** es un término de Azure Data Factory versión 2 que define una instancia de ejecución de una canalización. Por ejemplo, supongamos que tiene una canalización que se ejecute a las 8 a. m., 9 a. m. y 10 a. m. En este caso, habrá tres ejecuciones independientes de la canalización (ejecuciones de canalización). Cada ejecución de canalización tiene un id. exclusivo de ejecución de canalización, que es un GUID que define de forma exclusiva esa ejecución de canalización en particular. Normalmente las instancias de ejecuciones de canalización se crean al pasar argumentos a parámetros definidos en las canalizaciones. Hay dos maneras de ejecutar una canalización: **manualmente** o a través de un **desencadenador**. En este artículo se proporcionan detalles sobre ambas maneras de ejecutar una canalización. 

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en la versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Programación y ejecución de Data Factory V1](v1/data-factory-scheduling-and-execution.md).

## <a name="run-pipeline-on-demand"></a>Ejecución de una canalización a petición
En este método, ejecuta la canalización manualmente. También se considera una ejecución a petición de una canalización. 

Por ejemplo, supongamos que tiene una canalización denominada **copyPipeline** que desea ejecutar. La canalización es una canalización simple con una sola actividad que copia de una carpeta de origen en Azure Blob Storage a una carpeta de destino en el mismo almacenamiento. Esta es la definición de canalización del ejemplo: 

```json
{
  "name": "copyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "sourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```
La canalización toma dos parámetros: sourceBlobContainer y sinkBlobContainer, tal y como se muestra en la definición de JSON. Los valores se pasan a estos parámetros en tiempo de ejecución. 

Para ejecutar la canalización manualmente, puede usar una de las maneras siguientes: .NET, PowerShell, REST y Python. 

### <a name="rest-api"></a>API de REST
Este es un comando de REST de ejemplo:  

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```
Consulte el [Inicio rápido: Create a data factory using REST API](quickstart-create-data-factory-rest-api.md) (Crear una factoría de datos con la API de REST) para obtener un ejemplo completo.

### <a name="powershell"></a>PowerShell
Este es un comando de PowerShell de ejemplo: 

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

Los parámetros se pasan en el cuerpo de la carga de solicitud. En .NET, PowerShell y Python, los valores se pasan en un diccionario que se pasa como argumento a la llamada.

```json
{
  “sourceBlobContainer”: “MySourceFolder”,
  “sinkBlobCountainer”: “MySinkFolder”
}
```

La carga de respuesta es un identificador único de la ejecución de canalización:

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```


Consulte el [Inicio rápido: Create a data factory using PowerShell](quickstart-create-data-factory-powershell.md) (Crear una factoría de datos con PowerShell) para obtener un ejemplo completo.

### <a name="net"></a>.NET 
Esta es una llamada de .NET de ejemplo: 

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

Consulte el [Inicio rápido: Create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Crear una factoría de datos con .NET) para obtener un ejemplo completo.

> [!NOTE]
> Puede utilizar la API de .NET para invocar las canalizaciones de Data Factory desde Azure Functions, sus propios servicios web, etc.

## <a name="triggers"></a>Desencadenadores
Los desencadenadores ofrecen la segunda manera de ejecutar una ejecución de canalización. Los desencadenadores representan una unidad de procesamiento que determina cuándo es necesario poner en marcha una ejecución de canalización. Actualmente, Data Factory admite un desencadenador que invoca una canalización en una programación de reloj. Se llama **desencadenador de programador**. Actualmente, Data Factory no admite desencadenadores basados en eventos, como un desencadenador de una canalización que se ejecuta en el evento de la llegada de un archivo.

Las canalizaciones y los desencadenadores tienen una relación de varios a varios. Varios desencadenadores pueden dar comienzo a una única canalización o el mismo desencadenador puede iniciar varias canalizaciones. En la siguiente definición de JSON de un desencadenador, la propiedad **pipelines** hace referencia a una lista de canalizaciones que desencadena un desencadenador en particular, así como a los valores para parámetros de canalización.

### <a name="basic-trigger-definition"></a>Definición básica del desencadenador: 
```json
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {
            …
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
```

## <a name="scheduler-trigger"></a>Desencadenador de programador
El desencadenador de programador ejecuta canalizaciones según una programación de reloj. Este desencadenador admite las opciones de calendario periódicas y avanzadas (semanal, los lunes a las 5 p. m. y el jueves a las 9 p. m.). Es flexible, al ser independiente del patrón del conjunto de datos y no discernir entre los datos de serie temporal y no de serie temporal.

### <a name="scheduler-trigger-json-definition"></a>Definición JSON del desencadenador de programador
Cuando se crea un desencadenador de programador, puede especificar la programación y periodicidad mediante JSON, como se muestra en el ejemplo de esta sección. 

Para hacer que el desencadenador de programador dé inicio a una ejecución de canalización, incluya una referencia de canalización de la canalización en particular en la definición del desencadenador. Las canalizaciones y los desencadenadores tienen una relación de varios a varios. Varios desencadenadores pueden comenzar una única canalización. Un único desencadenador puede iniciar varias canalizaciones.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>,             // optional, how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC"
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": ": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
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


### <a name="overview-scheduler-trigger-schema"></a>Información general: esquema del desencadenador de programador
En la tabla siguiente se muestra una descripción general de los elementos más importantes relacionados con la periodicidad y la programación de un desencadenador:

Propiedad JSON |     Descripción
------------- | -------------
startTime | startTime es una fecha y hora. Para las programaciones sencillas, startTime es la primera aparición. Para las programaciones complejas, el desencadenador no se inicia antes de startTime.
endTime | Especifica la fecha y hora de finalización para el desencadenador. El desencadenador no se ejecuta después de esta fecha. No es válido tener un valor de endTime en el pasado.
timeZone | Actualmente, solo se admite UTC. 
recurrence | El objeto recurrence especifica las reglas de periodicidad para el desencadenador. El objeto recurrence admite los elementos siguientes: frequency, interval, endTime, count y schedule. Si se define recurrence, se requiere frequency; los otros elementos de recurrence son opcionales.
frequency | Representa la unidad de frecuencia con la que se repite el desencadenador. Los valores admitidos son: `minute`, `hour`, `day`, `week` o `month`.
interval | El intervalo es un entero positivo. Indica el intervalo de la frecuencia que determina cuán seguido se ejecuta el desencadenador. Por ejemplo, si interval es 3 y frequency es “week”, el desencadenador se repite cada tres semanas.
schedule | Un desencadenador con una frecuencia especificada modifica su periodicidad según una programación periódica. La propiedad schedule contiene modificaciones basadas en minutos, horas, días de la semana, días del mes y número de semana.


### <a name="schedule-trigger-example"></a>Ejemplo de desencadenador de programación

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-01T09:00:00-08:00",
                "endTime": "2017-11-02T22:00:00-08:00"
            }
        },
        "pipelines": [{
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToBlobPipeline"
                },
                "parameters": {}
            },
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToAzureSQLPipeline"
                },
                "parameters": {}
            }
        ]
    }
}
```

### <a name="overview-scheduler-trigger-schema-defaults-limits-and-examples"></a>Información general: valores predeterminados del esquema de desencadenador, límites y ejemplos

Nombre JSON | Tipo de valor | ¿Necesario? | Valor predeterminado | Valores válidos | Ejemplo
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | String | Sí | None | Fechas-horas ISO-8601 | ```"startTime" : "2013-01-09T09:30:00-08:00"```
recurrence | Objeto | Sí | None | Objeto de periodicidad | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
interval | Number | No | 1 | 1 a 1000. | ```"interval":10```
endTime | String | Sí | None | Valor de fecha y hora que representa un periodo de tiempo en el futuro | `"endTime" : "2013-02-09T09:30:00-08:00"`
schedule | Objeto | No | None | Objeto de programación | `"schedule" : { "minute" : [30], "hour" : [8,17] }`

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


Nombre JSON | Descripción | Valores válidos
--------- | ----------- | ------------
minutes | Minutos de la hora en la que se ejecuta el desencadenador. | <ul><li>Entero</li><li>Matriz de enteros</li></ul>
hours | Horas del día en la que se ejecuta el desencadenador. | <ul><li>Entero</li><li>Matriz de enteros</li></ul>
weekDays | Días de la semana en los que se ejecuta el desencadenador. Solo se puede especificar con una frecuencia semanal. | <ul><li>Lunes, martes, miércoles, jueves, viernes, sábado o domingo</li><li>Matriz de cualquiera de los valores anteriores (tamaño máximo de la matriz 7)</li></p>No distingue mayúsculas de minúsculas</p>
monthlyOccurrences | Determina los días del mes en los que se ejecutar el desencadenador. Solo se puede especificar con una frecuencia mensual. | Matriz de objetos de monthlyOccurrence: `{ "day": day,  "occurrence": occurence }`. <p> day es el día de la semana en el que se ejecutar el desencadenador; por ejemplo, `{Sunday}` es cada domingo del mes. Necesario.<p>El valor de occurrence es la repetición del día durante el mes, por ejemplo, `{Sunday, -1}` es el último domingo del mes. Opcional.
monthDays | Día del mes en el que se ejecutar el desencadenador. Solo se puede especificar con una frecuencia mensual. | <ul><li>Cualquier valor <= -1 y >= -31</li><li>Cualquier valor >= 1 y <= 31</li><li>Una matriz de valores</li>


## <a name="examples-recurrence-schedules"></a>Ejemplos: programaciones de periodicidad
En esta sección se muestran ejemplos de programaciones de periodicidad, centrándose en el objeto schedule y sus subelementos.

Todas las programaciones siguientes asumen que interval está establecido en 1. Además, suponga la frecuencia correcta de acuerdo con lo que está en schedule: por ejemplo, no puede usar la frecuencia “day” y tener una modificación “monthDays” en la programación. Estas restricciones se mencionan en la tabla de la sección anterior. 

Ejemplo | Descripción
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




## <a name="next-steps"></a>Pasos siguientes
Vea los siguientes tutoriales: 

- [Inicio rápido: create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Crear una factoría de datos mediante .NET)
