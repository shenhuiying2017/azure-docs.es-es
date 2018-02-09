---
title: Copia de datos de Google BigQuery con Azure Data Factory (beta) | Microsoft Docs
description: "Aprenda cómo copiar datos de Google BigQuery en almacenes de datos receptores compatibles mediante una actividad de copia en una canalización de Azure Data Factory."
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
ms.openlocfilehash: 3b559e64f38727b1e390160515b7614ad1dfaa97
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory-beta"></a>Copia de datos de Google BigQuery con Azure Data Factory (beta)

En este artículo se describe el uso de la actividad de copia de Azure Data Factory para copiar datos de Google BigQuery. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que presenta información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Actividad de copia en la versión 1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Este conector está actualmente en versión beta. Puede probarlo y enviarnos sus comentarios. No debe usarse en entornos de producción.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos de Google BigQuery en cualquier almacén de datos de receptor compatible. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

 Data Factory proporciona un controlador integrado para permitir la conectividad. Por lo tanto, no es necesario instalar manualmente uno para usar este conector.

## <a name="get-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector Google BigQuery.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de Google BigQuery:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en **GoogleBigQuery**. | Sí |
| proyecto | Identificador del proyecto predeterminado de BigQuery para el que se realizarán consultas.  | Sí |
| additionalProjects | Lista separada por comas de identificadores de proyectos públicos de BigQuery para su acceso.  | Sin  |
| requestGoogleDriveScope | Si desea solicitar acceso a Google Drive. Al permitir el acceso a Google Drive, se habilita la compatibilidad para las tablas federadas que combinan datos de BigQuery con datos de Google Drive. El valor predeterminado es **false**.  | Sin  |
| authenticationType | Mecanismo de autenticación OAuth 2.0 que se usa para autenticar. ServiceAuthentication solo puede usarse en Integration Runtime autohospedado. <br/>Los valores permitidos son: **ServiceAuthentication** y **UserAuthentication**. | Sí |
| refreshToken | El token de actualización obtenido de Google usado para autorizar el acceso a BigQuery para UserAuthentication. Este campo se puede marcar como SecureString para almacenarlo de forma segura en Data Factory. También puede almacenar la contraseña en Azure Key Vault y dejar que la actividad de copia la extraiga de ahí al realizar la copia de datos. Para más información, consulte [Almacenamiento de credenciales en Key Vault](store-credentials-in-key-vault.md). | Sin  |
| email | El identificador de correo electrónico de la cuenta de servicio que se usa para ServiceAuthentication. Solo se puede usar en Integration Runtime autohospedado.  | Sin  |
| keyFilePath | La ruta de acceso completa al archivo de clave. p12 que se usa para autenticar la dirección de correo electrónico de la cuenta de servicio. Solo se puede usar en Integration Runtime autohospedado.  | Sin  |
| trustedCertPath | La ruta de acceso completa del archivo .pem que contiene certificados de entidad de certificación de confianza usados para comprobar el servidor cuando se conecta a través de SSL. Esta propiedad solo puede establecerse cuando se usa SSL en Integration Runtime autohospedado. El valor predeterminado es el archivo cacerts.pem instalado con Integration Runtime.  | Sin  |
| useSystemTrustStore | Especifica si se usa un certificado de entidad de certificación del almacén de confianza del sistema o de un archivo .pem especificado. El valor predeterminado es **false**.  | Sin  |

**Ejemplo:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refresh token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). En esta sección se proporciona una lista de las propiedades compatibles con el conjunto de datos de Google BigQuery.

Para copiar datos de Google BigQuery, establezca la propiedad type del conjunto de datos en **GoogleBigQueryObject**. No hay ninguna propiedad específica de tipo adicional en este tipo de conjunto de datos.

**Ejemplo**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades compatibles con el tipo de origen de Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource como tipo de origen

Para copiar datos de Google BigQuery, establezca el tipo de origen de la actividad de copia en **GoogleBigQuerySource**. En la sección **source** de la actividad de copia se admiten las siguientes propiedades.

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del origen de la actividad de copia debe establecerse en **GoogleBigQuerySource**. | Sí |
| query | Use la consulta SQL personalizada para leer los datos. Un ejemplo es `"SELECT * FROM MyTable"`. | Sí |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.
