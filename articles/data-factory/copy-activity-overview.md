---
title: Actividad de copia en Azure Data Factory | Microsoft Docs
description: "Obtenga información sobre la actividad de copia en Azure Data Factory que puede usar para copiar datos desde un almacén de datos de origen compatible a un almacén de datos de receptor compatible."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: jingwang
ms.openlocfilehash: 2095d75ed042ae8be02ae0a1570f8e77d06a3563
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2018
---
# <a name="copy-activity-in-azure-data-factory"></a>Actividad de copia en Azure Data Factory

## <a name="overview"></a>Información general

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-data-movement-activities.md)
> * [Versión 2: versión preliminar](copy-activity-overview.md)

En Azure Data Factory, puede usar la actividad de copia para copiar datos entre los almacenes de datos locales y en la nube. Una vez copiados los datos, se pueden analizar y transformar con más profundidad. La actividad de copia también puede utilizarse para publicar los resultados de transformación y análisis de inteligencia empresarial (BI) y el consumo de la aplicación.

![Rol de actividad de copia](media/copy-activity-overview/copy-activity.png)

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Actividad de copia en V1](v1/data-factory-data-movement-activities.md).

La actividad de copia se ejecuta en una instancia de [Integration Runtime](concepts-integration-runtime.md). Para otro escenario de copia de datos, se puede usar otro tipo de Integration Runtime:

* Cuando copie datos entre almacenes de datos y ambos sean accesibles públicamente, la actividad de copia puede usar **Azure Integration Runtime**, que es seguro, confiable, escalable y con carácter de [disponibilidad global](concepts-integration-runtime.md#integration-runtime-location).
* Cuando copie datos con almacenes de datos como origen o destino ubicados en el entorno local o en una red con control de acceso (por ejemplo, Azure Virtual Network), debe configurar una instancia de **Integrated Runtime autohospedado** para impulsar la copia de datos.

Integration Runtime se debe asociar con todos los almacenes de datos receptores y de origen. Obtenga detalles sobre cómo la actividad de copia [determina el tipo de IR que se usará](concepts-integration-runtime.md#determining-which-ir-to-use).

La actividad de copia pasa por las siguientes fases para copiar datos de un origen a un receptor. El servicio que sustenta la actividad de copia realiza las siguientes acciones:

1. Lee datos desde un almacén de datos de origen.
2. Realiza procesos de serialización y deserialización, compresión y descompresión, asignación de columnas, etc. Lleva a cabo estas operaciones basadas en las configuraciones del conjunto de datos de entrada y de salida, y la actividad de copia.
3. Escribe datos en el almacén de datos de receptor o destino.

![Información general de la actividad de copia](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Almacenes de datos y formatos que se admiten

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Formatos de archivos admitidos

La actividad de copia se puede usar para **copiar archivos tal cual** entre dos almacenes de datos basados en archivos, en cuyo caso los datos se copian de manera eficaz sin serialización ni deserialización.

La actividad de copia también admite leer y escribir en archivos de formatos especificados: **texto, JSON, Avro, ORC y Parquet** y admite códecs de compresión **GZip, Deflate, BZip2 y ZipDeflate**. Consulte [Formatos de archivo y de compresión admitidos](supported-file-formats-and-compression-codecs.md) para más información.

Por ejemplo, puede realizar las siguientes actividades de copia:

* Copiar datos en el SQL Server local y escribirlos en Azure Data Lake Store en formato ORC.
* Copiar archivos en formato de texto (CSV) desde el sistema de archivos local y escribirlos en Blob de Azure en formato Avro.
* Copiar archivos comprimidos del sistema de archivos local y, a continuación, descomprimirlos en Azure Data Lake Store.
* Copiar datos en formato de texto comprimido GZip (CSV) desde Azure Blob y escribirlos en Azure SQL Database.

## <a name="supported-regions"></a>Regiones admitidas

El servicio que ofrece la actividad de copia está disponible globalmente en las regiones y zonas geográficas enumeradas en [Ubicaciones de Azure Integration Runtime](concepts-integration-runtime.md#integration-runtime-location). La topología disponible globalmente garantiza un movimiento de datos eficiente que, normalmente, evita saltos entre regiones. Consulte la sección [Servicios por región](https://azure.microsoft.com/regions/#services) para conocer la disponibilidad de Data Factory y el movimiento de datos en una región.

## <a name="configuration"></a>Configuración

Para usar la actividad de copia en Azure Data Factory, necesita:

1. **Crear servicios vinculados para el almacén de datos de origen y el almacén de datos de receptor**. Consulte la sección "Propiedades del servicio vinculado" del artículo sobre conectores para información de cómo configurar y las propiedades compatibles. Puede encontrar la lista de conectores compatibles en la sección [Almacenes de datos y formatos que se admiten](#supported-data-stores-and-formats).
2. **Crear conjuntos de datos para origen y receptor**. Consulte la sección "Propiedades del conjunto de datos" del artículo sobre el conector de origen y receptor para información de cómo configurar y las propiedades compatibles.
3. **Crear una canalización con la actividad de copia**. Se proporciona un ejemplo en la sección siguiente.  

### <a name="syntax"></a>Sintaxis

La plantilla siguiente de una actividad de copia contiene una lista exhaustiva de todas las propiedades compatibles. Especifique las que se adapten a su escenario.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "cloudDataMovementUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

### <a name="syntax-details"></a>Detalles de la sintaxis

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type de una actividad de copia se debe establecer en: **Copy** | Sí |
| inputs | Especifique el conjunto de datos que creó y que señala los datos de origen. La actividad de copia admite solo una entrada. | Sí |
| outputs | Especifique el conjunto de datos que creó y que señala los datos de receptor. La actividad de copia admite solo una salida. | Sí |
| typeProperties | Grupo de propiedades para configurar la actividad de copia. | Sí |
| de origen | Especifique el tipo de origen de copia y las propiedades correspondientes sobre cómo recuperar los datos.<br/><br/>Obtenga detalles en la sección "Propiedades de la actividad de copia" del artículo sobre conectores que aparece en [Almacenes de datos y formatos que se admiten](#supported-data-stores-and-formats). | Sí |
| sink | Especifique el tipo de receptor de copia y las propiedades correspondientes sobre cómo escribir los datos.<br/><br/>Obtenga detalles en la sección "Propiedades de la actividad de copia" del artículo sobre conectores que aparece en [Almacenes de datos y formatos que se admiten](#supported-data-stores-and-formats). | Sí |
| translator | Especifique asignaciones de columna explícitas de origen a receptor. Se aplica cuando el comportamiento predeterminado de copia no puede satisfacer sus necesidades.<br/><br/>Obtenga información en [Asignación de tipos de datos y esquemas](copy-activity-schema-and-type-mapping.md). | Sin  |
| cloudDataMovementUnits | Especifique la potencia de [Azure Integration Runtime](concepts-integration-runtime.md) para impulsar la copia de datos.<br/><br/>Obtenga información en [Unidades de movimiento de datos de nube](copy-activity-performance.md). | Sin  |
| parallelCopies | Especifique el paralelismo que desea que la actividad de copia use al leer datos desde el origen y copiar datos en el receptor.<br/><br/>Obtenga información en [Copia en paralelo](copy-activity-performance.md#parallel-copy). | Sin  |
| enableStaging<br/>stagingSettings | Elija almacenar provisionalmente los datos en una instancia de Blob Storage en lugar de copiar directamente los datos del origen al receptor.<br/><br/>Obtenga detalles sobre la configuración y los escenarios útiles en [Copia almacenada provisionalmente](copy-activity-performance.md#staged-copy). | Sin  |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Elija cómo controlar las filas incompatibles al copiar datos de origen a receptor.<br/><br/>Obtenga información en [Tolerancia a errores](copy-activity-fault-tolerance.md). | Sin  |

## <a name="monitoring"></a>Supervisión

Puede supervisar la ejecución de la actividad de copia en la interfaz de usuario "Author & Monitor" (Creación y supervisión) de Azure Data Factory o de manera programática. Luego, puede comparar el rendimiento y la configuración de su escenario con la [referencia de rendimiento](copy-activity-performance.md#performance-reference) de la actividad de copia desde pruebas internas.

### <a name="monitor-visually"></a>Supervisión visual

Para supervisar de manera visual la ejecución de la actividad de copia, vaya a la factoría de datos -> **Author & Monitor (Creación y supervisión)** -> **pestaña Monitor (Supervisión)**. Verá una lista de ejecuciones de canalización con un vínculo "View Activity Runs" (Ver ejecuciones de actividad) en la columna **Acciones**. 

![La supervisión de la canalización se ejecuta](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)

Haga clic para ver la lista de actividades de esta ejecución de canalización. En la columna **Actions** (Acciones), hay vínculos a la entrada, la salida, los errores (si se producen errores en la ejecución de la actividad de copia) y los detalles de la actividad de copia.

![Supervisión de las ejecuciones de actividad](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

Haga clic en el vínculo "**Detalles**" en **Acciones** para ver los detalles de la ejecución y las características de rendimiento de la actividad de copia. Muestra información, incluidos el volumen, las filas y los archivos de los datos copiados desde el origen al receptor, el rendimiento, los pasos que recorre con su duración correspondiente y las configuraciones usadas para el escenario de copia.

**Ejemplo: copia de Amazon S3 a Azure Data Lake Store**
![Detalles de la supervisión de la ejecución de actividad](./media/copy-activity-overview/monitor-activity-run-details-adls.png)

**Ejemplo: copia de Azure SQL Database a Azure SQL Data Warehouse con una copia almacenada provisionalmente**
![Detalles de la supervisión de la ejecución de actividad](./media/copy-activity-overview/monitor-activity-run-details-sql-dw.png)

### <a name="monitor-programmatically"></a>Supervisión mediante programación

Los detalles de la ejecución de la actividad de copia y las características de rendimiento también se devuelven en la sección Copy Activity run result -> Output (Resultado de la ejecución de la actividad de copia -> Salida). A continuación, aparece una lista completa. Solo se mostrarán los elementos que se aplican a su escenario de copia. Obtenga información sobre cómo supervisar la ejecución de activad en la [sección de supervisión de inicio rápido](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run).

| Nombre de propiedad  | DESCRIPCIÓN | Unidad |
|:--- |:--- |:--- |
| dataRead | Tamaño de los datos leídos desde el origen | Valor Int64 en **bytes** |
| dataWritten | Tamaño de los datos escritos en el receptor | Valor Int64 en **bytes** |
| filesRead | Número de archivos que se copia al copiar datos desde el almacenamiento de archivos. | Valor Int64 (sin unidad) |
| filesWritten | Número de archivos que se copia al copiar datos al almacenamiento de archivos. | Valor Int64 (sin unidad) |
| rowsCopied | Número de filas que se copian (no se aplica a la copia binaria). | Valor Int64 (sin unidad) |
| rowsSkipped | Número de filas incompatibles que se omiten. Puede establecer "enableSkipIncompatibleRow" en true para activar la característica. | Valor Int64 (sin unidad) |
| throughput | Velocidad de transferencia de los datos | Número de punto flotante en **KB/s** |
| copyDuration | Duración de la copia | Valor Int32 en segundos |
| sqlDwPolyBase | Si se usa PolyBase cuando se copian datos en SQL Data Warehouse. | boolean |
| redshiftUnload | Si se usa UNLOAD cuando se copian datos desde Redshift. | boolean |
| hdfsDistcp | Si se usa DistCp cuando se copian datos desde HDFS. | boolean |
| effectiveIntegrationRuntime | Muestra el tipo de Integration Runtime que se usa para impulsar la ejecución de actividad con el formato "`<IR name> (<region if it's Azure IR>)`". | Texto (cadena) |
| usedCloudDataMovementUnits | Las unidades de movimiento de datos de nube efectivas durante la copia. | Valor Int32 |
| usedParallelCopies | El número de parallelCopies efectivo durante la copia. | Valor Int32|
| redirectRowPath | Ruta de acceso al registro de las filas incompatibles omitidas en la instancia de Blob Storage que configura en "redirectIncompatibleRowSettings". Consulte el ejemplo siguiente. | Texto (cadena) |
| executionDetails | Más detalles sobre las fases por las que pasa la actividad de copia y los pasos, la duración, las configuraciones usadas, etc. correspondientes. No se recomienda analizar esta sección, porque podría modificarse. | Matriz |

```json
"output": {
    "dataRead": 107280845500,
    "dataWritten": 107280845500,
    "filesRead": 10,
    "filesWritten": 10,
    "copyDuration": 224,
    "throughput": 467707.344,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US 2)",
    "usedCloudDataMovementUnits": 32,
    "usedParallelCopies": 8,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureDataLakeStore"
            },
            "status": "Succeeded",
            "start": "2018-01-17T15:13:00.3515165Z",
            "duration": 221,
            "usedCloudDataMovementUnits": 32,
            "usedParallelCopies": 8,
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 219
            }
        }
    ]
}
```

## <a name="schema-and-data-type-mapping"></a>Asignación de tipo de datos y esquema

Consulte [Asignación de tipo de datos y esquema](copy-activity-schema-and-type-mapping.md), que describe cómo la actividad de copia asigna los datos de origen al receptor.

## <a name="fault-tolerance"></a>Tolerancia a errores

De manera predeterminada, la actividad de copia deja de copiar datos y devuelve un error cuando encuentra datos incompatibles entre el origen y el receptor. Puede configurar explícitamente la omisión y el registro de las filas incompatibles y solo copiar los datos compatibles para que la copia se realice correctamente. Consulte la [tolerancia a errores de la actividad de copia](copy-activity-fault-tolerance.md) para obtener más información.

## <a name="performance-and-tuning"></a>Rendimiento y optimización

Vea el artículo [Guía de optimización y rendimiento de la actividad de copia](copy-activity-performance.md), en el que se describen los factores claves que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory. También muestra el rendimiento observado durante las pruebas internas y trata diversas maneras de optimizar el rendimiento de la actividad de copia.

## <a name="incremental-copy"></a>Copia incremental 
La versión 2 de Data Factory es compatible con escenarios de copia incremental de datos diferenciales de un almacén de datos de origen en uno de destino. Consulte el [tutorial sobre la copia incremental de datos](tutorial-incremental-copy-overview.md). 

## <a name="read-and-write-partitioned-data"></a>Lectura y escritura de datos con particiones
En la versión 1, Azure Data Factory admitía la lectura y la escritura de datos con particiones por medio de las variables del sistema SliceStart, SliceEnd, WindowStart y WindowEnd. En la versión 2, puede lograr este comportamiento mediante un parámetro de canalización y la hora de inicio o programada del desencadenador como un valor del parámetro. Para más información, consulte [How to read and write large data files](how-to-read-write-partitioned-data.md) (Cómo leer o escribir datos con particiones).

## <a name="next-steps"></a>pasos siguientes
Consulte las guías de inicio rápido, los tutoriales y los ejemplos siguientes:

- [Copia de datos de una ubicación a otra en la misma instancia de Azure Blob Storage](quickstart-create-data-factory-dot-net.md)
- [Copia de datos de Azure Blob Storage a Azure SQL Database](tutorial-copy-data-dot-net.md)
- [Copia de datos de un servidor SQL Server local a Azure](tutorial-hybrid-copy-powershell.md)
