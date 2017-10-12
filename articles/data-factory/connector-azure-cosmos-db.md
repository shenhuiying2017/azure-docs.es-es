---
title: Copia de datos con Azure Cosmos DB como origen o destino mediante Data Factory | Microsoft Docs
description: "Obtenga información sobre cómo copiar datos desde cualquier almacén de datos de origen compatible a Azure Cosmos DB o desde Cosmos DB a cualquier almacén de receptor compatible mediante Data Factory."
services: data-factory, cosmosdb
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 7d914684a0ee5598cee7972b78c3ec6296184466
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-to-or-from-azure-cosmos-db-using-azure-data-factory"></a>Copia de datos con Azure Cosmos DB como origen o destino mediante Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: disponibilidad general](v1/data-factory-azure-documentdb-connector.md)
> * [Versión 2: versión preliminar](connector-azure-cosmos-db.md)

En este artículo se resume el uso de la actividad de copia de Azure Data Factory para copiar datos con Azure Cosmos DB (API de DocumentDB) como origen o destino. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en la versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea el artículo sobre [la versión 1 del conector de Azure Cosmos DB](v1/data-factory-azure-documentdb-connector.md).

## <a name="supported-scenarios"></a>Escenarios admitidos

Puede copiar datos desde Azure Cosmos DB a cualquier almacén de datos de receptor compatible, o desde cualquier almacén de datos de origen admitido a Azure Cosmos DB. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector de Azure Cosmos DB admite las siguientes funcionalidades:

- [API de DocumentDB](https://docs.microsoft.com/en-us/azure/cosmos-db/documentdb-introduction) de Cosmos DB.
- Importación o exportación de documentos JSON tal cual, o copia de datos con un conjunto de datos tabular como origen o destino, por ejemplo, base de datos SQL, archivos CSV, etc.

Para copiar datos tal cual con archivos JSON u otra colección de Cosmos DB como origen o destino, consulte [Import/Export JSON documents](#importexport-json-documents) (Importación o exportación de documentos JSON).

## <a name="getting-started"></a>Introducción
Puede crear una canalización con la actividad de copia mediante el SDK de .NET, el SDK de Python, Azure PowerShell, la API de REST o la plantilla de Azure Resource Manager. Consulte el [tutorial de actividad de copia](quickstart-create-data-factory-dot-net.md) para obtener instrucciones paso a paso sobre cómo crear una canalización con una actividad de copia.

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas de Azure Cosmos DB.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de Azure Cosmos DB:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en: **CosmosDb**. | Sí |
| connectionString |Especifique la información necesaria para conectarse a la base de datos de Azure Cosmos DB. Tenga en cuenta que debe especificar la información de la base de datos en la cadena de conexión como el ejemplo siguiente. Marque este campo como SecureString. |Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) (si el almacén de datos se encuentra en una red privada) o Azure Integration Runtime. Si no se especifica, se usará Azure Integration Runtime. |No |

**Ejemplo:**

```json
{
    "name": "CosmosDbLinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre conjuntos de datos. En esta sección se proporciona una lista de las propiedades que el conjunto de datos de Azure Cosmos DB admite.

Para copiar datos con Azure Cosmos DB como origen o destino, establezca la propiedad type del conjunto de datos en **DocumentDbCollection**. Se admiten las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos debe establecerse en: **DocumentDbCollection**. |Sí |
| collectionName |Nombre de la colección de documentos de Cosmos DB. |Sí |

**Ejemplo:**

```json
{
    "name": "CosmosDbDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Esquema de Data Factory

En los almacenes de datos sin esquemas como Azure Cosmos DB, la actividad de copia deduce el esquema de una de las maneras siguientes. Por lo tanto, a menos que desee [importar o exportar documentos JSON tal cual](#importexport-json-documents), el procedimiento recomendado consiste en especificar la estructura de datos en la sección **structure**.

1. Si especifica la estructura de los datos mediante la propiedad **structure** en la definición del conjunto de datos, el servicio Data Factory respeta esta como la estructura del esquema. En este caso, si una fila no contiene un valor para una columna, se le proporcionará un valor nulo.
2. Si no especifica la estructura de los datos mediante la propiedad **structure** en la definición del conjunto de datos, el servicio Data Factory deduce el esquema utilizando la primera fila en los datos. En este caso, si la primera fila no contiene el esquema completo, algunas columnas se pueden perder en el resultado de la operación de copia.

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que el receptor y el origen de Azure Cosmos DB admiten.

### <a name="azure-cosmos-db-as-source"></a>Azure Cosmos DB como origen

Para copiar datos desde Azure Cosmos DB, establezca el tipo de origen de la actividad de copia en **DocumentDbCollectionSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en: **DocumentDbCollectionSource**. |Sí |
| query |Especifique la consulta Cosmos DB para leer datos.<br/><br/>Ejemplo: `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |No <br/><br/>Si no se especifica, la instrucción SQL que se ejecuta: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Carácter especial para indicar que el documento está anidado y cómo aplanar el conjunto de resultados.<br/><br/>Por ejemplo, si una consulta de Cosmos DB devuelve un resultado anidado `"Name": {"First": "John"}`, la actividad de copia identificará el nombre de columna como "Name.First" con el valor "John" cuando nestedSeparator sea punto. |No (el valor predeterminado es punto `.`) |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Document DB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-as-sink"></a>Azure Cosmos DB como receptor

Para copiar datos desde Azure Cosmos DB, establezca el tipo de receptor de la actividad de copia en **DocumentDbCollectionSink**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en: **DocumentDbCollectionSink**. |Sí |
| nestingSeparator |Un carácter especial en el nombre de columna de origen que indica que el documento anidado es necesario. <br/><br/>Por ejemplo, `Name.First` en la estructura del conjunto de datos de salida genera la siguiente estructura JSON en el documento de Cosmos DB cuando nestedSeparator es punto: `"Name": {"First": "[value maps to this column from source]"}`. |No (el valor predeterminado es punto `.`) |
| writeBatchTimeout |Tiempo de espera para que la operación se complete antes de que se agote el tiempo de espera.<br/><br/>Los valores permitidos son: intervalos de tiempo. Ejemplo: "00:30:00" (30 minutos). |No |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyToCosmosDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink"
            }
        }
    }
]
```

## <a name="importexport-json-documents"></a>Importación o exportación de documentos JSON

Con este conector de Cosmos DB, le resultará muy sencillo

* Importar documentos JSON desde varios orígenes a Cosmos DB, incluido Azure Blob, Azure Data Lake Store y otros almacenes basados en archivos compatibles con Azure Data Factory.
* Exportar documentos JSON de la colección de Cosmos DB a varios almacenes basados en archivos.
* Copiar documentos entre dos colecciones de Cosmos DB tal cual.

Para lograr dicha copia independiente del esquema:

- En conjuntos de datos de Cosmos DB, no especifique la sección "structure"; en el origen o receptor de Cosmos DB de la actividad de copia, no especifique la propiedad "nestingSeparator".
- Al realizar operaciones de importación o exportación con archivos JSON, en el conjunto de datos del almacén de archivos correspondiente, especifique el tipo de formato como "JsonFormat" y la configuración "filePattern" correctamente (consulte la sección [JSON format](supported-file-formats-and-compression-codecs.md#json-format) (Formato JSON) para obtener más información) y, luego, no especifique la sección "structure" y omita la configuración de formato restante.

## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md##supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.