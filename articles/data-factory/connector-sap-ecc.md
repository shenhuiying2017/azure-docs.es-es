---
title: Copia de datos desde SAP ECC mediante Azure Data Factory | Microsoft Docs
description: "Obtenga información sobre cómo copiar datos desde SAP ECC en almacenes de datos receptores compatibles a través de una actividad de copia de una canalización de Azure Data Factory."
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
ms.date: 02/27/2018
ms.author: jingwang
ms.openlocfilehash: efca2c129a1c7b8aca6b879d6d1311c6be157be1
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
# <a name="copy-data-from-sap-ecc-using-azure-data-factory"></a>Copia de datos de SAP ECC mediante Azure Data Factory

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos de SAP ECC (SAP Enterprise Central Component). El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Actividad de copia en V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos de SAP ECC en cualquier almacén de datos de receptor admitido. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector SAP ECC admite las siguientes funcionalidades:

- Copia de datos de SAP ECC en SAP NetWeaver versión 7.0 y versiones posteriores. 
- Copia de datos de todos los objetos expuestos por los servicios de SAP ECC OData (por ejemplo, SAP Table/Views, BAPI, Data Extractors, etc.), o de los datos o IDOC que se envían a SAP PI que se pueden recibir como OData mediante adaptadores relativos.
- Copiar datos con la autenticación básica.

## <a name="prerequisites"></a>requisitos previos

Por lo general, SAP ECC expone las entidades mediante servicios de OData a través de la puerta de enlace de SAP. Para usar este conector SAP ECC, necesitará lo siguiente:

- **Configurar la puerta de enlace SAP**. Para los servidores con una versión de SAP NetWeaver posterior a 7.4, ya está instalada la puerta de enlace de SAP. En caso contrario, debe instalar una puerta de enlace insertada o un centro de puerta de enlace antes de exponer los datos de SAP EEC a través de los servicios de OData. Aprenda a configurar la puerta de enlace de SAP mediante la [guía de instalación](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Active y configure el servicio SAP OData**. Puede activar los servicios de OData a través de TCODE SICF en cuestión de segundos. También puede configurar qué objetos se deben exponer. Esta es una [guía paso a paso](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/) de ejemplo.

## <a name="getting-started"></a>Introducción

Puede crear una canalización con la actividad de copia mediante el SDK de .NET, el SDK de Python, Azure PowerShell, la API de REST o la plantilla de Azure Resource Manager. Consulte el [tutorial de actividad de copia](quickstart-create-data-factory-dot-net.md) para obtener instrucciones paso a paso para crear una canalización con una actividad de copia.

Las secciones siguientes proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector SAP ECC.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado SAP ECC:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en: **SapEcc** | Sí |
| URL | La dirección URL del servicio SAP ECC OData. | Sí |
| nombre de usuario | Nombre de usuario usado para conectarse a SAP ECC. | Sin  |
| contraseña | Contraseña de texto no cifrado que se usa para conectarse a SAP ECC. | Sin  |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) o Azure Integration Runtime (si el almacén de datos es accesible públicamente). Si no se especifica, se usará Azure Integration Runtime. |Sin  |

**Ejemplo:**

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData url e.g. http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de SAP ECC.

Para copiar datos desde SAP ECC, establezca la propiedad type del conjunto de datos en **SapEccResource**. Se admiten las siguientes propiedades:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| path | Ruta de acceso de la entidad de SAP ECC OData. | Sí |

**Ejemplo**

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typePoperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admite el origen de SAP ECC.

### <a name="sap-ecc-as-source"></a>SAP ECC como origen

Para copiar datos desde SAP ECC, establezca el tipo de origen de la actividad de copia en **SapEccSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del origen de la actividad de copia debe establecerse en: **SapEccSource** | Sí |
| query | Opciones de consulta de OData para filtrar los datos. Ejemplo: "$select=Name,Description&$top=10".<br/><br/>El conector de SAP ECC copia datos de la dirección URL combinada: (dirección URL especificada en el servicio vinculado) / (ruta de acceso especificada en el conjunto de datos)?(consulta especificada en el origen de la actividad de copia). Consulte el artículo sobre [componentes de URL de OData](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Sí |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-ecc"></a>Asignación de tipos de datos para SAP ECC

Al copiar datos desde SAP ECC, se utilizan las siguientes asignaciones de tipos de datos de OData para los datos de SAP ECC en los tipos de datos provisionales de Azure Data Factory. Consulte el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen al receptor.

| Tipo de datos de OData | Tipo de datos provisionales de Data Factory |
|:--- |:--- |:--- |
| Edm.Binary | string |
| Edm.Boolean | Booleano |
| Edm.Byte | string |
| Edm.DateTime | Datetime |
| Edm.Decimal | DECIMAL |
| Edm.Double | Doble |
| Edm.Single | Single |
| Edm.Guid | string |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | string |
| Edm.Time | timespan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Actualmente, no se admiten tipos de datos complejos.

## <a name="next-steps"></a>pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.
