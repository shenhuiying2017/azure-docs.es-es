---
title: Copia de datos de Salesforce Marketing Cloud con Azure Data Factory (beta) | Microsoft Docs
description: Obtenga información sobre cómo copiar datos desde Salesforce Marketing Cloud en almacenes de datos receptores compatibles a través de una actividad de copia de una canalización de Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: jingwang
ms.openlocfilehash: b8de35aef53458142e24224deb9886548fab221c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2018
ms.locfileid: "33206709"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory-beta"></a>Copia de datos de Salesforce Marketing Cloud con Azure Data Factory (beta)

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos desde una base de datos Salesforce Marketing Cloud. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Actividad de copia en V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Este conector está actualmente en versión beta. Puede probarlo y enviarnos sus comentarios. No debe usarse en entornos de producción.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos desde Salesforce Marketing Cloud a cualquier almacén de datos receptor compatible. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

Azure Data Factory proporciona un controlador integrado para habilitar la conectividad. Por lo tanto, no es necesario instalar manualmente ningún controlador mediante este conector.

## <a name="getting-started"></a>Introducción

Puede crear una canalización con la actividad de copia mediante el SDK de .NET, el SDK de Python, Azure PowerShell, la API de REST o la plantilla de Azure Resource Manager. Consulte el [tutorial de actividad de copia](quickstart-create-data-factory-dot-net.md) para obtener instrucciones paso a paso para crear una canalización con una actividad de copia.

Las secciones siguientes proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas para el conector Salesforce Marketing Cloud.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado Salesforce Marketing Cloud:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en: **SalesforceMarketingCloud**. | Sí |
| clientId | El identificador de cliente asociado a la aplicación Salesforce Marketing Cloud.  | Sí |
| clientSecret | El secreto de cliente asociado a la aplicación Salesforce Marketing Cloud. Puede elegir marcar este campo como SecureString para almacenarlo de forma segura en ADF, o almacenar la contraseña en Azure Key Vault y permitir que ADF copie la extracción de la actividad desde allí al realizar la copia de datos. Obtenga más información sobre el [Almacenamiento de credenciales en Key Vault](store-credentials-in-key-vault.md). | Sí |
| useEncryptedEndpoints | Especifica si los puntos de conexión de origen de datos se cifran mediante HTTPS. El valor predeterminado es true.  | Sin  |
| useHostVerification | Especifica si se requiere que el nombre de host del certificado del servidor coincida con el nombre de host del servidor al conectarse a través de SSL. El valor predeterminado es true.  | Sin  |
| usePeerVerification | Especifica si se debe verificar la identidad del servidor al conectarse a través de SSL. El valor predeterminado es true.  | Sin  |

**Ejemplo:**

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints" : true,
            "useHostVerification" : true,
            "usePeerVerification" : true
        }
    }
}

```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de Salesforce Marketing Cloud.

Para copiar datos desde y hacia Salesforce Marketing Cloud, establezca la propiedad type del conjunto de datos en **SalesforceMarketingCloudObject**. No hay ninguna propiedad específica de tipo adicional en este tipo de conjunto de datos.

**Ejemplo**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades admitidas por el origen y el receptor de Salesforce Marketing Cloud.

### <a name="salesforce-marketing-cloud-as-source"></a>Salesforce Marketing Cloud como origen

Para copiar datos desde y hacia Salesforce Marketing Cloud, establezca el tipo de origen de la actividad de copia en **SalesforceMarketingCloudObject**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del origen de la actividad de copia debe establecerse en: **SalesforceMarketingCloudSource** | Sí |
| query | Use la consulta SQL personalizada para leer los datos. Por ejemplo: `"SELECT * FROM MyTable"`. | Sí |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceMarketingCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SalesforceMarketingCloud input dataset name>",
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
                "type": "SalesforceMarketingCloudSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.
