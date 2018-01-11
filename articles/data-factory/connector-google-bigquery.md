---
title: Copiar datos de Google BigQuery con Azure Data Factory (beta) | Microsoft Docs
description: "Obtenga información sobre cómo copiar datos de Google BigQuery en almacenes de datos receptores compatibles a través de una actividad de copia de una canalización de Azure Data Factory."
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
ms.openlocfilehash: 00962b1bb32ff096712d36c07620505e72667380
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-google-bigquery-using-azure-data-factory-beta"></a>Copiar datos de Google BigQuery con Azure Data Factory (beta)

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos de Google BigQuery. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Actividad de copia en V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Este conector está actualmente en versión beta. Puede probarlo y enviarnos sus comentarios. No debe usarse en entornos de producción.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos de Google BigQuery en cualquier almacén de datos de receptor compatible. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

Azure Data Factory proporciona un controlador integrado para habilitar la conectividad. Por lo tanto, no es necesario instalar manualmente ningún controlador mediante este conector.

## <a name="getting-started"></a>Introducción

Puede crear una canalización con la actividad de copia mediante el SDK de .NET, el SDK de Python, Azure PowerShell, la API de REST o la plantilla de Azure Resource Manager. Consulte el [tutorial de actividad de copia](quickstart-create-data-factory-dot-net.md) para obtener instrucciones paso a paso sobre cómo crear una canalización con una actividad de copia.

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas para el conector Google BigQuery.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de Google BigQuery:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en: **GoogleBigQuery** | Sí |
| proyecto | Proyecto predeterminado de BigQuery para el que se realizarán consultas.  | Sí |
| additionalProjects | Lista separada por comas de proyectos públicos de BigQuery para su acceso.  | No |
| requestGoogleDriveScope | Si desea solicitar acceso a Google Drive. Al permitir el acceso a Google Drive, se habilita la compatibilidad para las tablas federadas que combinan datos de BigQuery con datos de Google Drive. El valor predeterminado es false.  | No |
| authenticationType | Mecanismo de autenticación OAuth 2.0 que se usa para autenticar. ServiceAuthentication solo puede utilizarse en IR autohospedados. <br/>Los valores permitidos son: **ServiceAuthentication** y **UserAuthentication**. | Sí |
| refreshToken | Token de actualización obtenido de Google para autorizar el acceso a BigQuery para UserAuthentication. Puede elegir marcar este campo como SecureString para almacenarlo de forma segura en ADF o almacenar la contraseña en Azure Key Vault y permitir que la actividad de copia incorpore los cambios desde allí al realizar la copia de datos. Obtenga más información sobre el [Almacenamiento de credenciales en Key Vault](store-credentials-in-key-vault.md). | No |
| email | Id. de correo electrónico de la cuenta de servicio usado para ServiceAuthentication y que solo puede utilizarse en IR autohospedados.  | No |
| keyFilePath | Ruta de acceso completa al archivo de clave. p12 que se usa para autenticar la dirección de correo electrónico de la cuenta de servicio y que solo puede utilizarse en IR autohospedados.  | No |
| trustedCertPath | Ruta de acceso completa del archivo .pem que contiene certificados de CA de confianza para comprobar el servidor al conectarse a través de SSL. Esta propiedad solo puede establecerse al utilizar SSL en IR autohospedados. El valor predeterminado es el archivo cacerts.pem instalado con el IR.  | No |
| useSystemTrustStore | Especifica si se utiliza un certificado de CA del almacén de confianza del sistema o de un archivo PEM especificado. El valor predeterminado es false.  | No |

**Ejemplo:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project>",
            "additionalProjects" : "<additionalProjects>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refreshToken>"
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

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades compatibles con el origen de Google BigQuery.

### <a name="googlebigquerysource-as-source"></a>GoogleBigQuerySource como origen

Para copiar datos de Google BigQuery, establezca el tipo de origen de la actividad de copia en **GoogleBigQuerySource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en: **GoogleBigQuerySource**. | Sí |
| query | Use la consulta SQL personalizada para leer los datos. Por ejemplo: `"SELECT * FROM MyTable"`. | Sí |

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

## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.
