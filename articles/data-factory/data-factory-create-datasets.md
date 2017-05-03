---
title: "Creación de conjuntos de datos en Azure Data Factory | Microsoft Docs"
description: Aprenda a crear conjuntos de datos en Data Factory de Azure con ejemplos que utilizan propiedades como offset y anchorDateTime.
keywords: crear conjunto de datos, ejemplo de conjunto de datos, ejemplo con offset
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 0614cd24-2ff0-49d3-9301-06052fd4f92a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: e0c999b2bf1dd38d8a0c99c6cdd4976cc896dd99
ms.openlocfilehash: 88e653f6e46f3e8eb72e620b495d1769f17bdfbf
ms.lasthandoff: 04/20/2017


---
# <a name="datasets-in-azure-data-factory"></a>Conjuntos de datos en Data Factory de Azure
En este artículo se describen conjuntos de datos de Data Factory de Azure y se incluyen ejemplos, como bases de datos offset, anchorDateTime y offset/style.

> [!NOTE]
> Si no está familiarizado con Azure Data Factory, consulte [Introducción a Azure Data Factory](data-factory-introduction.md) para obtener información general sobre este servicio. Si no tiene experiencia práctica con la creación de factorías de datos, repasar los tutoriales sobre [transformación](data-factory-build-your-first-pipeline.md) o [traslado de datos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) puede ayudarlo a comprender mejor este artículo. 

## <a name="overview"></a>Información general
Una factoría de datos puede tener una o más canalizaciones. Una **canalización** es una agrupación lógica de **actividades** que realizan una tarea. Las actividades de una canalización definen las acciones que se van a realizar en los datos. Por ejemplo, puede utilizar una actividad de copia para copiar datos de un servidor SQL Server local en una instancia de Azure Blob Storage. Después, utilice una actividad de Hive que ejecute un script de Hive en un clúster de Azure HDInsight para procesar o transformar datos de Blob Storage con el fin de generar datos de salida. Finalmente, use una segunda actividad de copia para copiar los datos de salida en una instancia de Azure SQL Data Warehouse en función de qué soluciones de generación de informes de inteligencia empresarial (BI) estén integradas. Para obtener más información sobre las canalizaciones y actividades, vea el artículo [Canalizaciones y actividades en Azure Data Factory](data-factory-create-pipelines.md).

Una actividad puede tomar diversos **conjuntos de datos** de entrada, o ninguno, y generar uno o varios conjuntos de datos de salida. Un conjunto de datos de entrada representa la entrada para una actividad de la canalización y un conjunto de datos de salida representa la salida de la actividad. Los conjuntos de datos identifican datos en distintos almacenes de datos, como tablas, archivos, carpetas y documentos. Por ejemplo, un conjunto de datos del blob de Azure especifica el contenedor de blobs y la carpeta de Azure Blob Storage de los que la canalización debe leer los datos. 

Antes de crear un conjunto de datos, debe crear un **servicio vinculado** para vincular su almacén de datos a la factoría de datos. Los servicios vinculados son muy similares a las cadenas de conexión que definen la información de conexión necesaria para que Data Factory se conecte a recursos externos. Los conjuntos de datos identifican datos en los almacenes de datos vinculados, como tablas, archivos, carpetas y documentos de SQL. Por ejemplo, el servicio vinculado Azure Storage vincula una cuenta de Azure Storage a la factoría de datos. Un conjunto de datos de blobs de Azure representa el contenedor de blobs y la carpeta que contiene los blobs de entrada que se van a procesar. 

Este es un escenario de ejemplo: para copiar datos desde una instancia de Azure Blob Storage en una instancia de Azure SQL Database, cree dos servicios vinculados, a saber, Azure Storage y Azure SQL Database. Después, cree dos conjuntos de datos: el conjunto de datos de Azure Blob (se refiere al servicio vinculado de Azure Storage) y el conjunto de datos de Azure SQL Table (se refiere al servicio vinculado de Azure SQL Database). Los servicios vinculados de Azure Storage y Azure SQL Database contienen cadenas de conexión que Data Factory usa en tiempo de ejecución para conectarse al almacenamiento de Azure y a la base de datos de Azure SQL, respectivamente. El conjunto de datos de Azure Blob especifica el contenedor de blobs y la carpeta de blobs que contiene los blobs de entrada de Azure Blob Storage. El conjunto de datos de Azure SQL Table especifica la tabla de SQL de Azure SQL Database en que se van a copiar los datos.

En el siguiente diagrama se muestra la relación entre la canalización, la actividad, el conjunto de datos y el servicio vinculado en Data Factory: 

![Relación entre la canalización, la actividad, el conjunto de datos y los servicios vinculados](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Conjunto de datos JSON
Un conjunto de datos en Azure Data Factory se define con formato JSON como sigue:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
       "policy":
        {      
        }
    }
}
```

La tabla siguiente describe las propiedades del JSON anterior:   

| Propiedad | Descripción | Obligatorio | Valor predeterminado |
| --- | --- | --- | --- |
| name |Nombre del conjunto de datos. Consulte [Data Factory de Azure: reglas de nomenclatura](data-factory-naming-rules.md) para ver este tipo de reglas. |Sí |N/D |
| type |Tipo de conjunto de datos. Especifique uno de los tipos admitidos por Data Factory de Azure (por ejemplo: AzureBlob, AzureSqlTable). <br/><br/>Para obtener información detallada, vea [Tipo de conjunto de datos](#Type). |Sí |N/D |
| structure |Esquema del conjunto de datos<br/><br/>Para obtener información detallada, vea [Estructura del conjunto de datos](#Structure). |No. |N/D |
| typeProperties | Las propiedades de tipo son diferentes para cada tipo (por ejemplo: Azure Blob o SQL Azure Table). Vea [Tipo de conjunto de datos](#Type) para obtener información detallada sobre los tipos admitidos y sus propiedades. |Sí |N/D |
| external | Marca booleana para especificar si un conjunto de datos es generado explícitamente por una canalización de la factoría de datos o no. Si la canalización actual no produce el conjunto de datos de entrada de una actividad, defina esta marca en true. Establezca esta marca en true para el conjunto de datos de entrada de la primera actividad de la canalización.  |No |false |
| availability | Define la ventana de procesamiento (por horas, por días, etc.) o el modelo de segmentación para la producción del conjunto de datos. Cada unidad de datos consumida y producida por la ejecución de una actividad se denomina segmento de datos. Si la disponibilidad de un conjunto de datos de salida se establece en diaria (frecuencia: día, intervalo: 1), se produce un segmento diariamente. <br/><br/>Para obtener información detallada, vea [Disponibilidad del conjunto de datos](#Availability). <br/><br/>Consulte el artículo [Programación y ejecución](data-factory-scheduling-and-execution.md) para más información. |Sí |N/D |
| policy |Define los criterios o la condición que deben cumplir los segmentos del conjunto de datos. <br/><br/>Para más información, consulte la sección [Directiva del conjunto de datos](#Policy) . |No |N/D |

## <a name="dataset-example"></a>Ejemplo de conjunto de datos
En el siguiente ejemplo, el conjunto de datos representa una tabla llamada **MyTable** en una **base de datos SQL de Azure**.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Tenga en cuenta los siguientes puntos:

* type está establecido en AzureSqlTable.
* La propiedad de tipo tableName (específico del tipo AzureSqlTable) se establece en MyTable.
* linkedServiceName hace referencia a un servicio vinculado de tipo AzureSqlDatabase, que se define en el siguiente fragmento JSON. 
* El valor de frecuency en availability se establece en Day y el intervalo en 1, lo cual significa que el segmento del conjunto de datos se produce diariamente.  

AzureSqlLinkedService se define como sigue:

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

En el ejemplo JSON anterior:

* type está establecido en AzureSqlDatabase
* La propiedad de tipo connectionString especifica información para conectarse a una Base de datos SQL de Azure.  

Como puede ver, el servicio vinculado define cómo conectarse a una base de datos SQL de Azure. El conjunto de datos define qué tabla se utiliza como entrada o salida de la actividad en una canalización.   

> [!IMPORTANT]
> A menos que se produzca un conjunto de datos mediante la canalización, debe marcarse como **external**. Esta configuración se aplica generalmente a las entradas de la primera actividad de una canalización.   


## <a name="Type"></a> Tipo de conjunto de datos
El tipo del conjunto de datos depende del almacén de datos que utilice. Vea la tabla siguiente para obtener una lista de almacenes de datos compatibles con Data Factory. Haga clic en un almacén de datos para obtener información sobre cómo crear un servicio vinculado y un conjunto de datos para ese almacén de datos.

[!INCLUDE [data-factory-supported-data-stores](../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Los almacenes de datos con * pueden ser locales o estar en la IaaS de Azure; además, requieren que instale [Data Management Gateway](data-factory-data-management-gateway.md) en una máquina local o de la IaaS de Azure.

En el ejemplo en la sección anterior, el tipo del conjunto de datos se establece en **AzureSqlTable**. De forma similar, para un conjunto de datos de Azure Blob, el tipo del conjunto de datos se establece en **AzureBlob**, tal y como se muestra en el siguiente esquema JSON:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

## <a name="Structure"></a>Estructura del conjunto de datos
La sección **structure** es una sección **opcional** que define el esquema del conjunto de datos. Contiene una colección de nombres y tipos de datos de columnas. Utilice la sección sobre la estructura para proporcionar información de tipo que se utiliza para convertir tipos y asignar columnas del origen al destino. En el ejemplo siguiente, el conjunto de datos tiene tres columnas (`slicetimestamp`, `projectname` y `pageviews`) cuyos tipos son String, String y Decimal respectivamente.

```json
structure:  
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

Cada columna de la estructura contiene las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| name |Nombre de la columna. |Sí |
| type |Tipo de datos de la columna.  |No |
| culture |Referencia cultural basada en .NET que se usará cuando se trate de un tipo .NET: `Datetime` o `Datetimeoffset`. El valor predeterminado es `en-us`. |No |
| formato |Cadena de formato que se usará cuando se trata de un tipo .NET: `Datetime` o `Datetimeoffset`. |No |

Utilice las siguientes directrices sobre cuándo incluir la información de la sección **structure** y qué incluir en ella.

* **Para los orígenes de datos estructurados** que almacenan el esquema de datos y la información de tipos junto con los propios datos (orígenes como SQL Server, Oracle, tablas de Azure, etc.), especifique la sección “structure” si desea asignar columnas de orígenes específicas a columnas de receptor y sus nombres no son iguales. 
  
    Dado que la información de tipo ya está disponible para orígenes de datos estructurados, no debe incluir información de tipo cuando se incluye la sección "structure".
* **En cuanto al esquema de los orígenes de datos de lectura (en concreto, el blob de Azure)**, puede optar por guardar los datos sin almacenar el esquema o la información de tipos con ellos. Para estos tipos de orígenes de datos, incluya la sección “structure” si desea asignar columnas de origen a columnas de receptor (o) cuando el conjunto de datos sea un conjunto de datos de entrada para una actividad de copia y los tipos de datos del conjunto de datos de origen deban convertirse a tipos nativos para el receptor. 
    
    Data factory admite los siguientes valores de tipo basados en .NET compatibles con CLS con el fin de proporcionar información de tipos en la sección “structure” del esquema de los orígenes de datos de lectura como el blob de Azure: Int16, Int32, Int64, Single, Double, Decimal, Byte[], Bool, String, Guid, Datetime, Datetimeoffset y Timespan.

Data Factory realiza automáticamente las conversiones de tipo al mover datos desde un almacén de datos de origen a un almacén de datos de receptor. 
  

## <a name="Availability"></a> Disponibilidad del conjunto de datos
En la sección **availability** de un conjunto de datos, se define la ventana de procesamiento (cada hora, diariamente, semanalmente, etc.) del conjunto de datos. Para más información sobre las ventanas de actividad, vea el artículo sobre [programación y ejecución](data-factory-scheduling-and-execution.md).

La sección de disponibilidad siguiente especifica que el conjunto de datos de salida se produce cada hora (o) que el conjunto de datos de entrada está disponible cada hora:

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

Si la canalización tiene las siguientes horas de inicio y finalización:  

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

El conjunto de datos de salida se genera cada hora dentro de las horas de inicio y finalización de la canalización. Por lo tanto, esta canalización genera cinco segmentos de conjuntos de datos, uno por cada ventana de actividad (00:00-01:00, 01:00-02:00, 02:00-03:00, 03:00-04:00 y 04:00-05:00). 

La tabla siguiente describe las propiedades que puede utilizar en la sección de disponibilidad:

| Propiedad | Descripción | Obligatorio | Valor predeterminado |
| --- | --- | --- | --- |
| frequency |Especifica la unidad de tiempo para la producción de segmentos del conjunto de datos.<br/><br/><b>Frecuencia admitida</b>: Minute, Hour, Day, Week, Month. |Sí |N/D |
| interval |Especifica un multiplicador para frecuencia<br/><br/>”Frequency x interval” determina la frecuencia con la que se produce el segmento.<br/><br/>Si necesita segmentar el conjunto de datos cada hora, establezca <b>frequency</b> en <b>hour</b> e <b>interval</b> en <b>1</b>.<br/><br/><b>Nota:</b> Si especifica Frequency como Minute, se recomienda establecer interval en no menos de 15. |Sí |N/D |
| style |Especifica si el segmento debe producirse al principio o al final del intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Si frequency se establece en Month y style se establece en EndOfInterval, el segmento se produce el último día del mes. Si style se establece en StartOfInterval, el segmento se produce el primer día del mes.<br/><br/>Si frequency se establece en Day y style se establece en EndOfInterval, el segmento se produce la última hora del día.<br/><br/>Si frequency se establece en Hour y style se establece en EndOfInterval, el segmento se produce al final de la hora. Por ejemplo, para un segmento para el período de 1 p.m. – 2 p.m., el segmento se producirá a las 2 p.m. |No |EndOfInterval |
| anchorDateTime |Define la posición absoluta en el tiempo usada por el programador para calcular los límites del segmento de conjunto de datos. <br/><br/><b>Nota:</b> Si AnchorDateTime tiene partes de fecha más pormenorizadas que la frecuencia, estas se omitirán. <br/><br/>Por ejemplo, si el valor de <b>interval</b> es <b>hourly</b> (frequency: hour e interval: 1) y <b>AnchorDateTime</b> contiene <b>minutes and seconds</b>, las partes <b>minutes and seconds</b> de AnchorDateTime no se tienen en cuenta. |No |01/01/0001 |
| Offset |Intervalo de tiempo en función del cual se desplazan el inicio y el final de todos los segmentos del conjunto de datos. <br/><br/><b>Nota:</b> Si se especifican anchorDateTime y offset, el resultado es el desplazamiento combinado. |No |N/D |

### <a name="offset-example"></a>Ejemplo de offset
De forma predeterminada, cada día (`"frequency": "Day", "interval": 1`) se inician los segmentos a las 24:00 UTC (medianoche). Si desea que, en su lugar, la hora de inicio sea a las 6:00 UTC, defina el desplazamiento como se muestra en el siguiente fragmento de código: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>Ejemplo de anchorDateTime
En el ejemplo siguiente, el conjunto de datos se produce una vez cada 23 horas. El primer segmento se inicia a la hora especificada en anchorDateTime, que se define en `2017-04-19T08:00:00` (hora UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Ejemplo de desplazamiento y estilo
El siguiente conjunto de datos es un conjunto de datos mensual y se produce el tercer día de cada mes a las 8:00 (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00",    
    "style": "StartOfInterval"
}
```

## <a name="Policy"></a>Directiva del conjunto de datos
En la sección **policy** de la definición del conjunto de datos se definen los criterios o condiciones que deben cumplir los segmentos del conjunto de datos.

### <a name="validation-policies"></a>Directivas de validación
| Nombre de la directiva | Descripción | Aplicado a | Obligatorio | Valor predeterminado |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Valida que los datos de un **blob de Azure** cumplen los requisitos de tamaño mínimo (en megabytes). |blob de Azure |No |N/D |
| minimumRows |Valida que los datos de una **base de datos SQL de Azure** o una **tabla de Azure** contienen el número mínimo de filas. |<ul><li>Base de datos SQL de Azure</li><li>tabla de Azure</li></ul> |No |N/D |

#### <a name="examples"></a>Ejemplos
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>Conjuntos de datos externos
Los conjuntos de datos externos son los que no son producidos por una canalización de ejecución en la factoría de datos. Si el conjunto de datos está marcado como **external**, puede definirse la directiva **ExternalData** para influir en el comportamiento de la disponibilidad de los segmentos del conjunto de datos.

A menos que se esté produciendo un conjunto de datos mediante Data Factory de Azure, debe marcarse como **external**. Generalmente, esta configuración se aplicará a las entradas de la primera actividad de una canalización a menos que se desee usar el encadenamiento de actividades o canalizaciones.

| name | Descripción | Obligatorio | Valor predeterminado |
| --- | --- | --- | --- |
| dataDelay |Tiempo de retraso de la comprobación de la disponibilidad de los datos externos para el segmento especificado. Por ejemplo, si los datos están disponibles cada hora, la comprobación de si los datos externos están realmente disponibles y si el segmento correspondiente está preparado puede retrasarse mediante dataDelay.<br/><br/>Solo se aplica a la hora actual.  Por ejemplo, si es la 1:00 p.m. y este valor es de 10 minutos, la validación se inicia a la 1:10 p.m.<br/><br/>Esta configuración no afecta a los segmentos en el pasado; los segmentos para los que se cumple Slice End Time + dataDelay < Now se procesan sin ningún retraso.<br/><br/>Las horas posteriores a las 23:59 horas se deben especificar con el formato `day.hours:minutes:seconds`. Por ejemplo, para especificar 24 horas, no use 24:00:00; en su lugar, use 1.00:00:00. Si usa 24:00:00, se tratará como 24 días (24.00:00:00). Para 1 día y 4 horas, especifique 1:04:00:00. |No |0 |
| retryInterval |El tiempo de espera entre un error y el siguiente reintento. Se aplica a la hora actual; si se ha producido algún error en el intento anterior, el siguiente intento se realiza después del período establecido en retryInterval. <br/><br/>Si ahora es la 1:00 p.m., empezaremos el primer intento. Si la duración para completar la primera comprobación de validación es 1 minuto y la operación produce un error, el siguiente reintento será a la 1:00 + 1 minuto (duración) + 1 minuto (intervalo de reintento) = 1:02 p.m. <br/><br/>En el caso de los segmentos en el pasado, no habrá ningún retraso. El reintento se producirá inmediatamente. |No |00:01:00 (1 minuto) |
| retryTimeout |El tiempo de espera de cada reintento.<br/><br/>Si esta propiedad se establece en 10 minutos, la validación se debe completar en 10 minutos. Si la validación tarda más de 10 minutos en realizarse, el reintento agotará el tiempo de espera.<br/><br/>Si se agota el tiempo de espera de todos los intentos de validación, el segmento se marcará como TimedOut. |No |00:10:00 (10 minutos) |
| maximumRetry |Número de veces que se va a comprobar la disponibilidad de los datos externos. El valor máximo permitido es 10. |No |3 |


## <a name="create-datasets"></a>Creación de conjuntos de datos
Puede crear conjuntos de datos mediante el uso de algunas de estas herramientas o SDK. 

- Asistente para copia. 
- Portal de Azure
- Visual Studio
- Azure PowerShell
- Plantilla del Administrador de recursos de Azure
- API de REST
- API de .NET

Vea los siguientes tutoriales para obtener instrucciones paso a paso sobre cómo crear canalizaciones y conjuntos de datos con una de estas herramientas o SDK.
 
- [Creación de una canalización con una actividad de transformación de datos](data-factory-build-your-first-pipeline.md)
- [Creación de una canalización con una actividad de movimiento de datos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Una vez que se haya creado o implementado la canalización, puede administrar y supervisar sus canalizaciones utilizando las hojas de Azure Portal o la aplicación de supervisión y administración. Vaya a los siguientes temas para obtener instrucciones paso a paso. 

- [Supervisión y administración de canalizaciones con las hojas de Azure Portal](data-factory-monitor-manage-pipelines.md)
- [Supervisión y administración de canalizaciones con la aplicación de supervisión y administración](data-factory-monitor-manage-app.md)


## <a name="scoped-datasets"></a>Conjuntos de datos limitados
Puede crear conjuntos de datos que se limitan a una canalización mediante la propiedad **datasets** . Estos conjuntos de datos solo los pueden usar las actividades dentro de esta canalización, pero no las actividades de otras canalizaciones. En el ejemplo siguiente se define una canalización con dos conjuntos de datos: InputDataset-rdc y OutputDataset-rdc, que se usarán dentro de la canalización:  

> [!IMPORTANT]
> Los conjuntos de datos con ámbito solo se admiten con canalizaciones de un solo uso (pipelineMode establecido en OneTime). Consulte la sección [Canalización de una vez](data-factory-scheduling-and-execution.md#onetime-pipeline) para más información.
>
>

```json
{
    "name": "CopyPipeline-rdc",
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
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": true,
                    "policy": {}
                }
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Pasos siguientes
- Para más información sobre las canalizaciones, vea el artículo sobre la [creación de canalizaciones](data-factory-create-pipelines.md). 
- Si quiere conocer más detalles sobre cómo programar y ejecutar canalizaciones en Azure Data Factory, consulte [este artículo](data-factory-scheduling-and-execution.md). 
