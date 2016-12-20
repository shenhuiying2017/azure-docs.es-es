---
title: "Programación y ejecución con Data Factory | Microsoft Docs"
description: "Obtenga información sobre los aspectos de programación y ejecución del modelo de aplicación de Factoría de datos de Azure."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 088a83df-4d1b-4ac1-afb3-0787a9bd1ca5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: febc8fef864f88fa07accf91efc9b87727a48b32
ms.openlocfilehash: 8b1029075178fbc591645a5fd6a112ad0a7f8b86


---
# <a name="data-factory-scheduling-and-execution"></a>Programación y ejecución de Data Factory
En este artículo se explican los aspectos de programación y ejecución del modelo de aplicación de Azure Data Factory. 

## <a name="prerequisites"></a>Requisitos previos
En este artículo se presupone que comprende los conceptos básicos del modelo de aplicación de Data Factory, como la actividad, las canalizaciones, los servicios vinculados y los conjuntos de datos. Para los conceptos básicos de Azure Data Factory, consulte los artículos siguientes:

* [Introducción al servicio Factoría de datos de Azure](data-factory-introduction.md)
* [Procesos](data-factory-create-pipelines.md)
* [Conjuntos de datos](data-factory-create-datasets.md) 

## <a name="schedule-an-activity"></a>Programación de una actividad
Con la sección de programación del JSON de actividad, puede especificar una programación recurrente para una actividad. Por ejemplo, puede programar una actividad cada hora de la manera siguiente:

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},  
```

![Ejemplo de programador](./media/data-factory-scheduling-and-execution/scheduler-example.png)

Como se muestra en el diagrama, al especificar una programación para la actividad se crea una serie de ventanas de saltos. Las ventanas de saltos son series de intervalos de tiempo de tamaño fijo, no superpuestos y contiguos. Estas ventanas de saltos lógicas para la actividad se denominan *ventanas de actividad*.

Para la ventana de actividad actualmente en ejecución, se puede acceder al intervalo de tiempo asociado a la ventana de actividad con las variables del sistema [WindowStart](data-factory-functions-variables.md#data-factory-system-variables) y [WindowEnd](data-factory-functions-variables.md#data-factory-system-variables) del JSON de actividad. Puede usar estas variables para distintos fines en el JSON de actividad. Por ejemplo, puede usarlas para seleccionar datos de conjuntos de datos de entrada y salida que representen datos de serie temporal.

La propiedad **scheduler** admite las mismas subpropiedades que la propiedad **availability** en un conjunto de datos. Consulte el artículo [Disponibilidad del conjunto de datos](data-factory-create-datasets.md#Availability) para más información. Ejemplos: Programación con un desplazamiento de tiempo específico o establecimiento del modo para alinear el procesamiento al principio o al final del intervalo de la ventana de actividad.

Puede especificar propiedades de **programador** para una actividad, pero esta propiedad es **opcional**. Si especifica una propiedad, debe coincidir con el ritmo que indique en la definición del conjunto de datos de salida. Actualmente, el conjunto de datos de salida es lo que controla la programación, por lo que debe crear un conjunto de datos de salida aunque la actividad no genere ninguna salida. Si la actividad no toma ninguna entrada, puede omitir la creación del conjunto de datos de entrada.

## <a name="time-series-datasets-and-data-slices"></a>Conjuntos de datos y segmentos de datos de series temporales
Los datos de series temporales son una secuencia continua de puntos de datos que consisten normalmente en mediciones sucesivas realizadas en un intervalo de tiempo. Ejemplos comunes de datos de series temporales incluyen datos de sensores y datos de telemetría de aplicación.

Con Data Factory, puede procesar datos de series temporales por lotes con ejecuciones de actividad. Normalmente, hay un ritmo periódico en el que llegan los datos de entrada y los datos de salida tienen que producirse. Este ritmo se modela especificando la sección **availability** en el conjunto de datos de la manera siguiente:

```json
"availability": {
  "frequency": "Hour",
  "interval": 1
},
```

Cada unidad de datos consumida y producida por la ejecución de una actividad se denomina segmento de datos. El diagrama siguiente muestra un ejemplo de una actividad con un conjunto de datos de entrada y uno de salida. Estos conjuntos de datos tienen establecida la sección **availability** en una frecuencia horaria.

![Programador de disponibilidad](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

En el diagrama anterior se muestran los segmentos de datos por hora para el conjunto de datos de entrada y salida. El diagrama muestra tres segmentos de entrada que están listos para su procesamiento. La actividad 10-11 AM está en curso, produciendo el segmento de salida 10-11 AM.

Puede acceder al intervalo de tiempo asociado al segmento actual que se está produciendo en el JSON del conjunto de datos con las variables [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) y [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables).

Actualmente, Data Factory requiere que el programa especificado en la actividad coincida exactamente con el programa especificado en **availability** del conjunto de datos de salida. Por tanto, **WindowStart**, **WindowEnd**, **SliceStart** y **SliceEnd** siempre se asignan al mismo período y a un segmento de salida único.

Para más información sobre las diferentes propiedades disponibles para la sección de disponibilidad, consulte [creación de conjuntos de datos](data-factory-create-datasets.md).

## <a name="move-data-from-sql-database-to-blob-storage"></a>Mover datos de SQL Database a Blob Storage
Vamos a juntar todo y ponerlo en marcha mediante la creación de una canalización que copia datos de una tabla de Azure SQL Database en Azure Blob Storage cada hora.

**Entrada: conjunto de datos de Azure SQL Database**

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

El valor **frequency** está establecido en **Hour** y **interval** está establecido en **1** en la sección de disponibilidad.

**Salida: conjunto de datos de Azure Blob Storage**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "%M"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "%d"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "%H"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

El valor **frequency** está establecido en **Hour** y **interval** está establecido en **1** en la sección de disponibilidad.

**Actividad: actividad de copia**

```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                   "scheduler": {
                      "frequency": "Hour",
                      "interval": 1
                }
            }
        ],
        "start": "2015-01-01T08:00:00Z",
        "end": "2015-01-01T11:00:00Z"
    }
}
```

En el ejemplo se muestran las secciones de programación de la actividad y de disponibilidad del conjunto de datos establecidas en una frecuencia de cada hora. En el ejemplo se muestra cómo puede usar **WindowStart** y **WindowEnd** para seleccionar datos pertinentes para la ejecución de una actividad y copiarlos en un blob con el valor de **folderPath** adecuado. **folderPath** se parametriza para tener una carpeta independiente para cada hora.

Cuando se ejecutan tres de los segmentos entre las 8 y las 11 a.m., los datos de Azure SQL Database son los siguientes:

![Entrada de ejemplo](./media/data-factory-scheduling-and-execution/sample-input-data.png)

Tras implementar la canalización, el blob de Azure se rellena de la manera siguiente:

* Archivo mypath/2015/1/1/8/Data.&lt;Guid&gt;.txt con datos
    ```  
    10002345,334,2,2015-01-01 08:24:00.3130000
    10002345,347,15,2015-01-01 08:24:00.6570000
    10991568,2,7,2015-01-01 08:56:34.5300000
    ```
  
  > [!NOTE]
  > &lt;Guid&gt; se sustituye por un GUID real. Nombre del archivo de ejemplo: Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
  > 
  > 
* Archivo mypath/2015/1/1/9/Data.&lt;Guid&gt;.txt con datos:

    ```json  
    10002345,334,1,2015-01-01 09:13:00.3900000
    24379245,569,23,2015-01-01 09:25:00.3130000
    16777799,21,115,2015-01-01 09:47:34.3130000
    ```
* Archivo mypath/2015/1/1/10/Data.&lt;Guid&gt;.txt sin datos.

## <a name="active-period-for-pipeline"></a>Período activo de canalización
El artículo sobre la [creación de canalizaciones](data-factory-create-pipelines.md) introdujo el concepto de período activo para una canalización especificada mediante la configuración de las propiedades **start** y **end**.

Puede establecer la fecha de inicio para el período activo de la canalización en el pasado. Data Factory calcula automáticamente (rellena hacia atrás) todos los segmentos de datos en el pasado y empieza a procesarlos.

## <a name="parallel-processing-of-data-slices"></a>Procesamiento en paralelo de segmentos de datos
Puede configurar segmentos de datos de relleno de fondo para que se ejecuten en paralelo estableciendo la propiedad **concurrency** en la sección de directivas de la actividad JSON. Para obtener más información sobre esta propiedad, vea [Creación de canalizaciones](data-factory-create-pipelines.md).

## <a name="rerun-a-failed-data-slice"></a>Volver a ejecutar un segmento de datos con errores
Puede supervisar la ejecución de segmentos de manera visual enriquecida. Consulte [Supervisión y administración de canalizaciones mediante hojas de Azure Portal](data-factory-monitor-manage-pipelines.md) o [Aplicación de supervisión y administración](data-factory-monitor-manage-app.md) para más información.

Observe el ejemplo siguiente, que muestra dos actividades. Activity1 produce un conjunto de datos de series temporales con segmentos de salida que se han consumido como entrada por Activity2 para generar el conjunto de datos de series temporales de salida final.

![Segmento con errores](./media/data-factory-scheduling-and-execution/failed-slice.png)

El diagrama muestra que en tres segmentos recientes se produjo un error al generar el segmento 9-10 AM para Dataset2. Data Factory realiza automáticamente el seguimiento de la dependencia para el conjunto de datos de series temporales. Como resultado, no se inicia la ejecución de actividad para el segmento 9-10 AM de bajada.

Las herramientas de administración y supervisión de Data Factory permiten profundizar en los registros de diagnóstico del segmento con error para encontrar fácilmente la causa principal del problema y solucionarlo. Una vez solucionado el problema, puede iniciar fácilmente la ejecución de la actividad para generar el segmento con error. Para más información sobre cómo repetir la ejecución y entender las transiciones de estado para segmentos de datos, consulte el artículo [Supervisión y administración de canalizaciones de Azure Data Factory](data-factory-monitor-manage-pipelines.md) o [Aplicación de supervisión y administración](data-factory-monitor-manage-app.md).

Tras haber repetido la ejecución del segmento 9-10 AM para **Dataset2**, Data Factory inicia la ejecución del segmento dependiente de 9-10 AM en el conjunto de datos final.

![Repetición de ejecución de un segmento con errores](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="run-activities-in-a-sequence"></a>Ejecución de actividades en una secuencia
Puede encadenar dos actividades (ejecutar una después de otra) haciendo que el conjunto de datos de salida de una actividad sea el conjunto de datos de entrada de la otra actividad. Las actividades pueden estar en la misma canalización o en canalizaciones diferentes. La segunda actividad se ejecuta solo cuando la primera de ellas se completa correctamente.

Por ejemplo, considere el siguiente caso:

1. La canalización P1 incluye la actividad A1 que requiere el conjunto de datos de entrada externo D1 y genera el conjunto de datos de salida D2.
2. La canalización P2 incluye la actividad A2 que requiere una entrada del conjunto de datos D2 y genera el conjunto de datos de salida D3.

En este escenario, las actividades A1 y A2 son canalizaciones diferentes. La actividad A1 se ejecuta cuando los datos externos están disponibles y se alcanza la frecuencia de disponibilidad programada. La actividad A2 se ejecuta cuando están disponibles los segmentos programados de D2 y se alcanza la frecuencia de disponibilidad programada. Si se produce un error en uno de los segmentos del conjunto de datos D2, A2 no se ejecuta para ese segmento hasta que está disponible.

La vista Diagrama tendría un aspecto similar al siguiente diagrama:

![Encadenamiento de las actividades de dos canalizaciones](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Tal y como se ha mencionado anteriormente, las actividades pueden estar en la misma canalización. La vista Diagrama con ambas actividades en la misma canalización tendría un aspecto similar al siguiente diagrama:

![Encadenamiento de las actividades de la misma canalización](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

### <a name="copy-sequentially"></a>Copia secuencial
Es posible ejecutar varias operaciones de copia sucesivas de manera secuencial y ordenada. Por ejemplo, podría tener dos actividades de copia en una canalización (CopyActivity1 y CopyActivity2) con los siguientes conjuntos de datos de entrada y salida:   

CopyActivity1

Entrada: Dataset. Salida: Dataset2.

CopyActivity2

Entrada: Dataset2.  Salida: Dataset3.

ActividadCopia2 solo se ejecutaría si ActividadCopia1 se hubiera ejecutado correctamente y ConjuntoDatos2 estuviera disponible.

Esta es el JSON de canalización de ejemplo:

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

Observe que en el ejemplo, el conjunto de datos de salida de la primera actividad de copia (ConjuntoDatos2) se especifica como entrada para la segunda. Por lo tanto, la segunda actividad solo se ejecuta cuando el conjunto de datos de salida de la primera está listo.  

En el ejemplo, CopyActivity2 puede tener una entrada distinta, como Dataset3, pero especifica Dataset2 como una entrada de CopyActivity2 para que la actividad no se ejecute hasta que se haya completado CopyActivity1. Por ejemplo:

CopyActivity1

Entrada: Dataset1. Salida: Dataset2.

CopyActivity2

Entradas: Dataset3, Dataset2. Salida: Dataset4.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

Observe que en el ejemplo, hay dos conjuntos de datos de entrada especificados para la segunda actividad de copia. Cuando se especifican varias entradas, solo se usa el primer conjunto de datos de entrada para copiar los datos. Sin embargo, los demás conjuntos de datos se usan como dependencias. CopyActivity2 empezaría solo después de que se cumplen las condiciones siguientes:

* ActividadCopia1 se ha completado correctamente y ConjuntoDatos2 está disponible. Este conjunto de datos no se usa al copiar datos en Dataset4. Solo actúa como una dependencia de programación de ActividadCopia2.   
* ConjuntoDatos3 está disponible. Este conjunto de datos representa los datos que se copian en el destino.  

## <a name="model-datasets-with-different-frequencies"></a>Modelado de conjuntos de datos con distintas frecuencias
En los ejemplos, las frecuencias de los conjuntos de datos de entrada y salida y de la ventana de programación de actividad eran las mismas. Algunos escenarios requieren que se puedan producir resultados a una frecuencia diferente de las frecuencias de una o más entradas. Data Factory admite el modelado de estos escenarios.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Ejemplo 1: Generación de un informe de salida diario para los datos de entrada que esté disponibles cada hora
Considere un escenario en el que tiene datos de medida de entrada de sensores disponibles cada hora en Azure Blob Storage. Quiere generar un informe agregado diario con estadísticas como media, máximo y mínimo para el día con la [actividad de Hive de Data Factory](data-factory-hive-activity.md).

A continuación, se muestra cómo puede modelar este escenario con Data Factory:

**Conjunto de datos de entrada**

Se quitan los archivos de entrada de cada hora en la carpeta para el día especificado. La disponibilidad para la entrada se establece en **Hour** (frecuencia: hora, intervalo: 1).

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Conjunto de datos de salida**

Cada día se crea un archivo de salida en la carpeta del día. La disponibilidad de la salida se establece en **Day** (frecuencia: día e intervalo: 1).

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Actividad: actividad de Hive en una canalización**

El script de Hive recibe la información adecuada de *DateTime* como parámetros que usan la variable **WindowStart** como se muestra a continuación. El script de Hive usa esta variable para cargar los datos de la carpeta correcta para el día y ejecutar la agregación para generar la salida.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
            "inputs": [
                {
                    "name": "AzureBlobInput"
                }
            ],
            "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
                    "Month": "$$Text.Format('{0:%M}',WindowStart)",
                    "Day": "$$Text.Format('{0:%d}',WindowStart)"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },            
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 2,
                "timeout": "01:00:00"
            }
         }
     ]
   }
}
```

El diagrama siguiente muestra el escenario desde el punto de vista de la dependencia de datos.

![Dependencia de los datos](./media/data-factory-scheduling-and-execution/data-dependency.png)

El segmento de salida para cada día depende de 24 segmentos por hora del conjunto de datos de entrada. Data Factory calcula automáticamente estas dependencias al determinar los segmentos de datos de entrada que se encuentran en el mismo período de tiempo que el segmento de salida que se va a producir. Si cualquiera de los 24 segmentos de entrada no está disponible, Data Factory espera a que el segmento de entrada esté listo antes de empezar la ejecución de la actividad diaria.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Ejemplo 2: Especificación de la dependencia con expresiones y funciones de Data Factory
Consideremos otro escenario. Suponga que tiene una actividad de Hive que procesa dos conjuntos de datos de entrada. Uno de ellos tiene nuevos datos diariamente, pero otro obtiene datos nuevos cada semana. Supongamos que desea combinar las dos entradas y producir una salida cada día.

El enfoque sencillo, en el que Data Factory determina automáticamente los segmentos de entrada correctos que se van a procesar al alinearlos con el período de los segmentos de datos de salida, no funciona.

Debe especificar que para cada ejecución de actividad, Data Factory debe usar el segmento de datos de la semana pasada para el conjunto de datos de entrada semanal. Utilice funciones de Azure Data Factory, como se muestra en el siguiente fragmento para implementar este comportamiento.

**Input1: blob de Azure**

La primera entrada es el blob de Azure que se actualiza diariamente.

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Input2: blob de Azure**

Input2 es el blob de Azure que se actualiza semanalmente.

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**Salida: blob de Azure**

Cada día se crea un archivo de salida en la carpeta del día. La disponibilidad de la salida se establece en **day** (frecuencia: día, intervalo: 1).

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Actividad: actividad de Hive en una canalización**

La actividad de Hive toma las dos entradas y genera un segmento de salida cada día. Puede especificar de la forma siguiente el segmento de salida de cada día para que dependa de segmento de entrada de la semana pasada para la entrada semanal.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
            "Month": "$$Text.Format('{0:%M}',WindowStart)",
            "Day": "$$Text.Format('{0:%d}',WindowStart)"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        },            
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 2,  
          "timeout": "01:00:00"
        }
       }
     ]
   }
}
```

## <a name="data-factory-functions-and-system-variables"></a>Funciones y variables del sistema de Data Factory
Para conocer la lista de funciones y variables del sistema que admite Data Factory, consulte el artículo [Azure Data Factory: funciones y variables del sistema](data-factory-functions-variables.md) .

## <a name="data-dependency-deep-dive"></a>Profundización en la dependencia de datos
Con el fin de generar un segmento del conjunto de datos mediante la ejecución de una actividad, Data Factory usa el siguiente *modelo de dependencia* para determinar las relaciones entre los conjuntos de datos usados y los generados por una actividad.

El intervalo de tiempo de los conjuntos de datos de entrada necesario para generar el segmento del conjunto de datos de salida se denomina *período de dependencia*.

La ejecución de una actividad genera un segmento del conjunto de datos solo después de que estén disponibles los segmentos de datos de los conjuntos de datos de entrada dentro del período de dependencia. En otras palabras, todos los segmentos de entrada que comprenden el período de dependencia deben estar en el estado **Listo** para que la ejecución de la actividad genere un segmento de conjunto de datos de salida.

Para generar el segmento de conjunto de datos [**start**, **end**], se necesita una función para asignar el segmento de conjunto de datos a su período de dependencia. Esta función es básicamente una fórmula que convierte el principio y el final del segmento del conjunto de datos en el comienzo y final del período de dependencia. Dicho más formalmente,

```
DatasetSlice = [start, end]
DependecyPeriod = [f(start, end), g(start, end)]
```

**F** y **g** son funciones de asignación que calculan el inicio y el fin del período de dependencia para cada entrada de actividad.

Tal como se ve en los ejemplos, el período de dependencia es el mismo que el período en el que se va a producir el segmento de datos. En estos casos, Data Factory calcula automáticamente los segmentos de entrada que se encuentran en el período de dependencia.  

Por ejemplo, en el ejemplo de agregación, en el que la salida se produce diariamente y los datos de entrada están disponibles cada hora, el período del segmento de datos es de 24 horas. Data Factory busca los segmentos de entrada relevantes de cada hora para este período de tiempo y hace que el segmento de salida sea dependiente del segmento de entrada.

También puede proporcionar su propia asignación para el período de dependencia, como se muestra en el ejemplo, donde una de las entradas es semanal y el segmento de salida se produce diariamente.

## <a name="data-dependency-and-validation"></a>Dependencia y validación de datos
Un conjunto de datos puede tener una directiva de validación definida que especifique cómo se pueden validar los datos generados por la ejecución de un segmento antes de que esté listo para su uso. Consulte el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md) para más información.

En estos casos, cuando el segmento ha terminado de ejecutarse, el estado del mismo cambia a **En espera** con un subestado de **Validación**. Una vez validados los segmentos, el estado del segmento cambia a **Listo**.

Si se ha generado un segmento de datos, pero no ha pasado la validación, no se procesan las ejecuciones de actividad de los segmentos de nivel inferior que dependen de ese segmento.

[Supervisión y administración de canalizaciones](data-factory-monitor-manage-pipelines.md) se tratan los diversos estados de los segmentos de datos en Data Factory.

## <a name="external-data"></a>Datos externos
Un conjunto de datos se puede marcar como externo (como se muestra en el fragmento de JSON), lo que implica que no se generó con Azure Data Factory. En tal caso, la directiva de conjunto de datos puede tener un conjunto de parámetros adicional que describe la validación adicional y la directiva de reintento para el conjunto de datos. Consulte [Creación de canalizaciones](data-factory-create-pipelines.md) para ver una descripción de todas las propiedades.

De forma similar a los conjuntos de datos que produce Data Factory, los segmentos de datos externos deben estar preparados antes de que se puedan procesar los segmentos dependientes.

```json
{
    "name": "AzureSqlInput",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1     
        },
        "external": true,
        "policy":
        {
            "externalData":
            {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }  
    }
}
```
## <a name="onetime-pipeline"></a>Canalización de una vez
Puede crear y programar una canalización que se ejecute periódicamente (por ejemplo, cada hora y diariamente) entre las horas de inicio y finalización que especifique en la definición de la canalización. Para más información, consulte [Programación de actividades](#scheduling-and-execution) . También puede crear una canalización que se ejecute una sola vez. Para ello, establezca la propiedad **pipelineMode** en **onetime** en la definición de la canalización,como se muestra en el siguiente ejemplo de JSON. El valor predeterminado de esta propiedad es **scheduled**.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ]
                "name": "CopyActivity-0"
            }
        ]
        "pipelineMode": "OneTime"
    }
}
```

Tenga en cuenta lo siguiente:

* No se especifican las horas de **inicio** y **finalización** de la canalización.
* La disponibilidad (**availability**) de los conjuntos de datos de entrada y salida se especifica (**frequency** e **interval**), incluso aunque Data Factory no use los valores.  
* La vista Diagrama no muestra las canalizaciones de una vez. Este comportamiento es así por diseño.
* Las canalizaciones de una vez no se pueden actualizar. Puede clonar una canalización de una vez, cambiarle el nombre, actualizar las propiedades e implementarla para crear otra.




<!--HONumber=Nov16_HO3-->


