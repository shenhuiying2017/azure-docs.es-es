---
title: Copia de datos desde y hacia SAP Cloud for Customer mediante Azure Data Factory | Microsoft Docs
description: Aprenda a copiar datos de SAP Cloud for Customer a almacenes de datos receptores compatibles o de almacenes de datos de origen compatibles a SAP Cloud for Customer mediante Data Factory.
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
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: e580c3f36ce19679d3edcf7a8861e4e492dfa9c5
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Copia de datos desde SAP Cloud for Customer (C4C) mediante Azure Data Factory

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos con SAP Cloud for Customer (C4C) como origen o destino. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Actividad de copia en V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos desde SAP Cloud for Customer a cualquier almacén de datos receptor, o desde cualquier almacén de datos de origen compatible a SAP Cloud for Customer. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector permite a Azure Data Factory copiar los datos con SAP Cloud for Customer como origen o destino, incluidas las soluciones SAP Cloud for Sales, SAP Cloud for Service y SAP Cloud for Social Engagement.

## <a name="getting-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector SAP Cloud for Customer.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado SAP Cloud for Customer:

| Propiedad | DESCRIPCIÓN | Requerido |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en: **SapCloudForCustomer**. | Sí |
| URL | Dirección URL del servicio de OData de SAP C4C. | Sí |
| Nombre de usuario | Especifique el nombre de usuario para conectarse a SAP C4C. | Sí |
| contraseña | Especifique la contraseña de la cuenta de usuario que se especificó para el nombre de usuario. Marque este campo como SecureString. | Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Si no se especifica, se usará Azure Integration Runtime. | No para el origen, sí para el receptor |

>[!IMPORTANT]
>Para copiar datos en SAP Cloud for Customer, [cree un IR de Azure](create-azure-integration-runtime.md#create-azure-ir) explícitamente con una ubicación próxima a SAP Cloud for Customer y asocie el servicio vinculado como en el ejemplo siguiente:

**Ejemplo:**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de SAP Cloud for Customer.

Para copiar datos de SAP Cloud for Customer, establezca la propiedad type del conjunto de datos en **SapCloudForCustomerResource**. Se admiten las siguientes propiedades:

| Propiedad | DESCRIPCIÓN | Requerido |
|:--- |:--- |:--- |
| Tipo | La propiedad type del conjunto de datos debe establecerse en: **SapCloudForCustomerResource**. |Sí |
| path | Especifique la ruta de acceso de la entidad de OData de SAP C4C. |Sí |

**Ejemplo:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typePoperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admite el origen de SAP Cloud for Customer.

### <a name="sap-c4c-as-source"></a>SAP C4C como origen

Para copiar datos de SAP Cloud for Customer, establezca el tipo de origen en la actividad de copia en **SapCloudForCustomerSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Requerido |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en: **SapCloudForCustomerSource**.  | Sí |
| query | Especifique la consulta de OData personalizada para leer datos. | Sin  |

Consulta de ejemplo para obtener datos de un día concreto: `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SAPC4CSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>SAP C4C como receptor

Para copiar datos de SAP Cloud for Customer, establezca el tipo de receptor en la actividad de copia en **SapCloudForCustomerSink**. Se admiten las siguientes propiedades en la sección **sink** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Requerido |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en: **SapCloudForCustomerSink**.  | Sí |
| writeBehavior | El comportamiento de escritura de la operación. Puede ser "Insert" o "Update". | Nº El valor predeterminado es "Insert". |
| writeBatchSize | Tamaño de lote de la operación de escritura. El tamaño de lote para obtener un rendimiento óptimo puede ser diferente para otra tabla o servidor. | Nº El valor predeterminado es 10. |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "cloudDataMovementUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>Asignación de tipos de datos para SAP Cloud for Customer

Al copiar datos desde SAP Cloud for Customer, se usan las siguientes asignaciones de tipos de datos de SAP Cloud for Customer en los tipos de datos provisionales de Azure Data Factory. Consulte el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen al receptor.

| Tipo de datos de OData de SAP C4C | Tipo de datos provisionales de Data Factory |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Booleano |
| Edm.Byte | Byte[] |
| Edm.DateTime | Datetime |
| Edm.Decimal | DECIMAL |
| Edm.Double | Doble |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | string |
| Edm.Time | timespan |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="next-steps"></a>pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.
