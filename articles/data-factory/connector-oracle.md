---
title: Copia de datos con Oracle como origen o destino mediante Azure Data Factory | Microsoft Docs
description: "Obtenga información sobre cómo copiar datos desde almacenes de origen compatibles a una base de datos Oracle (o) desde Oracle a almacenes de receptor compatibles mediante Data Factory."
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
ms.openlocfilehash: 10db7959396b4ee9927e4272dec9939ac8c13580
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-from-and-to-oracle-using-azure-data-factory"></a>Copia de datos con Oracle como origen o destino mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-onprem-oracle-connector.md)
> * [Versión 2: versión preliminar](connector-oracle.md)

En este artículo se resume el uso de la actividad de copia de Azure Data Factory para copiar datos con una base de datos Oracle como origen o destino. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea el artículo sobre [la versión 1 del conector Oracle](v1/data-factory-onprem-oracle-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos desde una base de datos Oracle a cualquier almacén de datos de receptor compatible o copiar datos desde cualquier almacén de datos de origen compatibles a una base de datos Oracle. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector Oracle admite las versiones siguientes de base de datos Oracle y es compatible con las autenticaciones Básica u OID.

- Oracle 12c R1 (12.1)
- Oracle 11g R1, R2 (11.1, 11.2)
- Oracle 10g R1, R2 (10.1, 10.2)
- Oracle 9i R1, R2 (9.0.1, 9.2)
- Oracle 8i R3 (8.1.7)

## <a name="prerequisites"></a>Requisitos previos

Para copiar datos con una base de datos Oracle como origen o destino que no es accesible públicamente, debe configurar un entorno Integration Runtime autohospedado. Consulte el artículo sobre [Integration Runtime autohospedado](create-self-hosted-integration-runtime.md) para más información sobre Integration Runtime. Integration Runtime proporciona un controlador Oracle integrado, por lo tanto, no es necesario instalar manualmente los controladores cuando se copian datos con Oracle como origen o destino.

## <a name="getting-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Las secciones siguientes proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector Oracle.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado Oracle:

| Propiedad | DESCRIPCIÓN | Requerido |
|:--- |:--- |:--- |
| Tipo | La propiedad type se debe establecer en: **Oracle** | Sí |
| connectionString | Especifique la información necesaria para conectarse a la instancia de Oracle Database. Marque este campo como SecureString.<br><br>**Tipo de conexión que se admite**: para identificar su base de datos, puede elegir usar el **SID de Oracle** o el **nombre de servicio de Oracle**:<br>- Usar el SID: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- Usar el nombre de servicio: `Host=<host>;Port=<port>;ServiceName=<sid>;User Id=<username>;Password=<password>;` | Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) o Azure Integration Runtime (si el almacén de datos es accesible públicamente). Si no se especifica, se usará Azure Integration Runtime. |Sin  |

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

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre conjuntos de datos. En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de Oracle.

Para copiar datos con Oracle como origen o destino, establezca la propiedad type del conjunto de datos en **OracleTable**. Se admiten las siguientes propiedades:

| Propiedad | DESCRIPCIÓN | Requerido |
|:--- |:--- |:--- |
| Tipo | La propiedad type del conjunto de datos se debe establecer en: **OracleTable** | Sí |
| tableName |Nombre de la tabla en la instancia de Base de datos de Oracle a la que hace referencia el servicio vinculado. | Sí |

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

### <a name="oracle-as-source"></a>Oracle como origen

Para copiar datos desde Oracle, establezca el tipo de origen de la actividad de copia en **OracleSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Requerido |
|:--- |:--- |:--- |
| Tipo | La propiedad type del origen de la actividad de copia debe establecerse en: **OracleSource** | Sí |
| oracleReaderQuery | Use la consulta SQL personalizada para leer los datos. Por ejemplo: `"SELECT * FROM MyTable"`. | Sin  |

Si no se especifica "oracleReaderQuery", las columnas que se define en la sección "structure" del conjunto de datos se usan para construir una consulta (`select column1, column2 from mytable`) para ejecutarla en la base de datos Oracle. Si la definición del conjunto de datos no tiene la sección "structure", se seleccionan todas las columnas de la tabla.

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

### <a name="oracle-as-sink"></a>Oracle como receptor

Si va a copiar datos en Oracle, establezca el tipo de receptor de la actividad de copia en **OracleSink**. Se admiten las siguientes propiedades en la sección **sink** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Requerido |
|:--- |:--- |:--- |
| Tipo | La propiedad type del receptor de la actividad de copia debe establecerse en: **OracleSink**. | Sí |
| writeBatchSize | Inserta datos en la tabla SQL cuando el tamaño del búfer alcanza el valor writeBatchSize.<br/>Los valores permitidos son: enteros (número de filas). |No (el valor predeterminado es 10 000) |
| writeBatchTimeout | Tiempo de espera para que la operación de inserción por lotes se complete antes de que se agote el tiempo de espera.<br/>Los valores permitidos son: intervalos de tiempo. Ejemplo: 00:30:00 (30 minutos). | Sin  |
| preCopyScript | Especifique una consulta SQL para que la actividad de copia se ejecute antes de escribir datos en Oracle en cada ejecución. Puede usar esta propiedad para limpiar los datos cargados previamente. | Sin  |

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

Al copiar datos con Oracle como origen o destino, se usan las siguientes asignaciones de tipos de datos de Oracle en los tipos de datos provisionales de Azure Data Factory. Consulte el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen al receptor.

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
Consulte los [almacenes de datos compatibles](copy-activity-overview.md##supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.