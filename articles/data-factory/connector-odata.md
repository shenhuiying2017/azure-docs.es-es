---
title: "Copia de datos desde orígenes OData mediante Azure Data Factory | Microsoft Docs"
description: "Obtenga información sobre cómo copiar datos desde orígenes OData a almacenes de datos receptores compatibles a través de una actividad de copia de una canalización de Azure Data Factory."
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
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 7346b4a146a228efdc3824ba989f3de77a4df8ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-from-odata-source-using-azure-data-factory"></a>Copia de datos desde orígenes OData mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-odata-connector.md)
> * [Versión 2: Versión preliminar](connector-odata.md)

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos con un origen OData como origen o destino. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en la versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea el artículo sobre [la versión 1 del conector OData](v1/data-factory-odata-connector.md).

## <a name="supported-scenarios"></a>Escenarios admitidos

Puede copiar datos desde un origen OData a cualquier almacén de datos receptor compatible. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector OData admite las siguientes funcionalidades:

- La **versión 3.0 y 4.0** de OData.
- Copiar datos mediante las autenticaciones siguientes: **Anónima**, **Básica** y **Windows**.

## <a name="getting-started"></a>Introducción
Puede crear una canalización con la actividad de copia mediante el SDK de .NET, el SDK de Python, Azure PowerShell, la API de REST o la plantilla de Azure Resource Manager. Consulte el [tutorial de actividad de copia](quickstart-create-data-factory-dot-net.md) para obtener instrucciones paso a paso sobre cómo crear una canalización con una actividad de copia.

Las secciones siguientes proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector OData.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de OData:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en **OData** |Sí |
| url | Dirección URL raíz del servicio de OData. |Sí |
| authenticationType | Tipo de autenticación que se usa para conectarse al origen de OData.<br/>Los valores posibles son: **Anónima**, **Básica** y **Windows**. OAuth no es compatible. | Sí |
| userName | Especifique el nombre de usuario si usa la autenticación Basic o Windows. | No |
| Contraseña | Especifique la contraseña de la cuenta de usuario que se especificó para el nombre de usuario. Marque este campo como SecureString. | No |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) (si el almacén de datos se encuentra en una red privada) o Azure Integration Runtime. Si no se especifica, se usará Azure Integration Runtime. |No |

**Ejemplo 1: Uso de autenticación anónima**

```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Ejemplo 2: Uso de la autenticación básica**

```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

**Ejemplo 3: Uso de autenticación de Windows**

```json
{
    "name": "ODataLinkedService",
    "properties":
    {
        "type": "OData",
        "typeProperties":
        {
            "url": "<endpoint of on-premises OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
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

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre conjuntos de datos. En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de OData.

Para copiar datos desde OData, establezca la propiedad type del conjunto de datos en **ODataResource**. Se admiten las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos debe establecerse en: **ODataResource**. | Sí |
| path | Ruta de acceso al recurso de OData. | No |

**Ejemplo**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admite el origen OData.

### <a name="odata-as-source"></a>OData como origen

Para copiar datos desde OData, establezca el tipo de origen de la actividad de copia como **RelationalSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en: **RelationalSource** | Sí |
| query | Opciones de consulta de OData para filtrar los datos. Ejemplo: "?$select=Name,Description&$top=5".<br/><br/>Observe el último; el conector OData copia datos de la dirección URL combinada: `[url specified in linked service]/[path specified in dataset][query specified in copy activity source]`. Consulte el artículo sobre [componentes de URL de OData](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | No |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "RelationalSource",
                "query": "?$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-odata"></a>Asignación de tipos de datos de OData

Al copiar datos desde OData, se utilizan las siguientes asignaciones de tipos de datos de OData en los tipos de datos provisionales de Azure Data Factory. Vea el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para obtener información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen en el receptor.

| Tipo de datos de OData | Tipo de datos provisionales de Data Factory |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Booleano |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Doble |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | Datetimeoffset |

> [!Note]
> No se admiten tipos de datos complejos de OData (por ejemplo, Object).


## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md##supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.