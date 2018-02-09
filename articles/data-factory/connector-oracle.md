---
title: Copia de datos con Oracle como origen o destino mediante Azure Data Factory | Microsoft Docs
description: "Obtenga información sobre cómo copiar datos desde almacenes de origen compatibles a una base de datos Oracle o desde Oracle a almacenes de receptor compatibles mediante Data Factory."
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
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: d6b96bc40325d398c91e293ec6ca8f8cc2993e58
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Copia de datos con Oracle como origen o destino mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1: ya está disponible con carácter general](v1/data-factory-onprem-oracle-connector.md)
> * [Versión 2: versión preliminar](connector-oracle.md)

En este artículo se resume el uso de la actividad de copia de Azure Data Factory para copiar datos con una base de datos Oracle como origen o destino. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que presenta información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si utiliza la versión 1 de Data Factory, que está disponible con carácter general, consulte el artículo sobre el [conector de Oracle en la versión 1](v1/data-factory-onprem-oracle-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos desde una base de datos de Oracle en cualquier almacén de datos receptor compatible. También puede copiar datos desde cualquier almacén de datos de origen compatible a una base de datos de Oracle. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector Oracle admite las versiones siguientes de base de datos Oracle. También admite la autenticación básica o mediante OID:

- Oracle 12c R1 (12.1)
- Oracle 11g R1, R2 (11.1, 11.2)
- Oracle 10g R1, R2 (10.1, 10.2)
- Oracle 9i R1, R2 (9.0.1, 9.2)
- Oracle 8i R3 (8.1.7)

## <a name="prerequisites"></a>requisitos previos

Para copiar datos con una base de datos Oracle como origen o destino que no es accesible públicamente, debe configurar un entorno de ejecución de integración autohospedado. Para más información acerca de un entorno de ejecución de integración, consulte el artículo sobre [Entorno de ejecución de integración autohospedado](create-self-hosted-integration-runtime.md). El entorno de ejecución de integración proporciona un controlador de Oracle integrado. Por tanto, no es necesario instalar manualmente un controlador para copiar datos con Oracle como origen y destino.

## <a name="get-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Las secciones siguientes proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector Oracle.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado Oracle.

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type se debe establecer en: **Oracle**. | Sí |
| connectionString | Especifica la información necesaria para conectarse a la instancia de Oracle Database. Marque este campo como SecureString.<br><br>**Tipo de conexión que se admite**: para identificar su base de datos, puede usar el **SID de Oracle** o el **nombre de servicio de Oracle**:<br>- Si usa el SID: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- Si usa el nombre del servicio: `Host=<host>;Port=<port>;ServiceName=<sid>;User Id=<username>;Password=<password>;` | Sí |
| connectVia | El [entorno de ejecución de integración](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) o Azure Integration Runtime (si el almacén de datos es accesible públicamente). Si no se especifica, se usará Azure Integration Runtime. |Sin  |

**Ejemplo:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
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

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de Oracle.

Para copiar datos con Oracle como origen o destino, establezca la propiedad type del conjunto de datos en **OracleTable**. Se admiten las siguientes propiedades.

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del conjunto de datos se debe establecer en: **OracleTable**. | Sí |
| tableName |El nombre de la tabla de la base de datos Oracle a la que hace referencia el servicio vinculado. | Sí |

**Ejemplo:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admiten el receptor y el origen Oracle.

### <a name="oracle-as-a-source-type"></a>Oracle como tipo de origen

Para copiar datos desde Oracle, establezca el tipo de origen de la actividad de copia en **OracleSource**. En la sección **source** de la actividad de copia se admiten las siguientes propiedades.

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del origen de la actividad de copia debe establecerse en: **OracleSource**. | Sí |
| oracleReaderQuery | Use la consulta SQL personalizada para leer los datos. Un ejemplo es `"SELECT * FROM MyTable"`. | No  |

Si no se especifica "oracleReaderQuery", las columnas que se definen en la sección "structure" del conjunto de datos se usan para construir una consulta (`select column1, column2 from mytable`) para ejecutarla en la base de datos Oracle. Si la definición del conjunto de datos no tiene la sección "structure", se seleccionan todas las columnas de la tabla.

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-a-sink-type"></a>Oracle como tipo de receptor

Si va a copiar datos en Oracle, establezca el tipo de receptor de la actividad de copia en **OracleSink**. En la sección **sink** de la actividad de copia se admiten las siguientes propiedades.

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del receptor de la actividad de copia debe establecerse en: **OracleSink**. | Sí |
| writeBatchSize | Inserta datos en la tabla SQL cuando el tamaño del búfer alcanza el valor writeBatchSize.<br/>Los valores permitidos son: enteros (número de filas). |No (el valor predeterminado es 10 000) |
| writeBatchTimeout | Tiempo de espera para que la operación de inserción por lotes se complete antes de que se agote el tiempo de espera.<br/>Los valores permitidos son intervalos de tiempo. Un ejemplo es 00:30:00 (30 minutos). | No  |
| preCopyScript | Especifique una consulta SQL para que la actividad de copia se ejecute antes de escribir datos en Oracle en cada ejecución. Puede usar esta propiedad para limpiar los datos cargados previamente. | No  |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-oracle"></a>Asignación de tipos de datos para Oracle

Al copiar datos con Oracle como origen o destino, se usan las siguientes asignaciones de tipos de datos de Oracle en los tipos de datos provisionales de Azure Data Factory. Para más información acerca de la forma en que la actividad de copia asigna el tipo de datos y el esquema de origen al receptor, consulte el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de datos de Oracle | Tipo de datos provisionales de Data Factory |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(solo se admite en Oracle 10g y versiones posteriores) |
| CHAR |string |
| CLOB |string |
| DATE |Datetime |
| FLOAT |Decimal, String (si la precisión > 28) |
| INTEGER |Decimal, String (si la precisión > 28) |
| LONG |string |
| LONG RAW |Byte[] |
| NCHAR |string |
| NCLOB |string |
| NUMBER |Decimal, String (si la precisión > 28) |
| NVARCHAR2 |string |
| RAW |Byte[] |
| ROWID |string |
| TIMESTAMP |Datetime |
| TIMESTAMP WITH LOCAL TIME ZONE |string |
| TIMESTAMP WITH TIME ZONE |string |
| UNSIGNED INTEGER |NUMBER |
| VARCHAR2 |string |
| XML |string |

> [!NOTE]
> Los tipos de datos INTERVAL YEAR TO MONTH e INTERVAL DAY TO SECOND no son compatibles.


## <a name="next-steps"></a>pasos siguientes
Para ver la lista de almacenes de datos que la actividad de copia de Data Factory admite como orígenes y receptores consulte [Almacenes de datos y formatos que se admiten](copy-activity-overview.md##supported-data-stores-and-formats).