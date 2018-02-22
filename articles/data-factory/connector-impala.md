---
title: Copia de datos de Impala con Azure Data Factory (beta) | Microsoft Docs
description: "Aprenda a copiar datos de Impala en almacenes de datos receptores compatibles mediante una actividad de copia en una canalización de factoría de datos."
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: e1f745fc70395f06d2eb3d98644d54c314a0ef26
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-impala-by-using-azure-data-factory-beta"></a>Copia de datos de Impala con Azure Data Factory (beta)

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos de Impala. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que presenta información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 de Data Factory, que está disponible con carácter general, consulte el artículo sobre la [actividad de copia de la versión 1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Este conector está actualmente en versión beta. Puede probarlo y enviarnos sus comentarios. No debe usarse en entornos de producción.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos de Impala en cualquier almacén de datos receptor admitido. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

 Data Factory proporciona un controlador integrado para permitir la conectividad. Por lo tanto, no es necesario instalar manualmente uno para usar este conector.

## <a name="get-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector de Impala.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de Impala:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe estar establecida en **Impala**. | Sí |
| host | La dirección IP o el nombre de host del servidor de Impala (es decir, 192.168.222.160).  | Sí |
| puerto | Puerto TCP que el servidor de Impala usa para escuchar las conexiones del cliente. El valor predeterminado es 21050.  | Sin  |
| authenticationType | Tipo de autenticación que se debe usar. <br/>Los valores permitidos son: **Anonymous**, **SASLUsername** y **UsernameAndPassword**. | Sí |
| Nombre de usuario | Nombre de usuario que se usa para acceder al servidor de Impala. El valor predeterminado es anonymous cuando se usa SASLUsername.  | Sin  |
| contraseña | La contraseña que se corresponde con el nombre de usuario cuando se usa UsernameAndPassword. Marque este campo como SecureString para almacenarlo de forma segura en Data Factory o [para hacer referencia a un secreto almacenado en Azure Key Vault](store-credentials-in-key-vault.md). | Sin  |
| enableSsl | Especifica si las conexiones al servidor se cifran mediante SSL. El valor predeterminado es **false**.  | Sin  |
| trustedCertPath | La ruta de acceso completa del archivo .pem que contiene certificados de entidad de certificación de confianza usados para comprobar el servidor cuando se conecta a través de SSL. Esta propiedad solo puede establecerse cuando se usa SSL en Integration Runtime autohospedado. El valor predeterminado es el archivo cacerts.pem instalado con Integration Runtime.  | Sin  |
| useSystemTrustStore | Especifica si se utiliza un certificado de CA del almacén de confianza del sistema o de un archivo PEM especificado. El valor predeterminado es **false**.  | Sin  |
| allowHostNameCNMismatch | Especifica si se requiere que el nombre del certificado SSL emitido por una entidad de certificación coincida con el nombre de host del servidor al conectarse a través de SSL. El valor predeterminado es **false**.  | Sin  |
| allowSelfSignedServerCert | Especifica si se permiten los certificados autofirmados del servidor. El valor predeterminado es **false**.  | Sin  |
| connectVia | El [entorno de ejecución de integración](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) o Azure Integration Runtime (si el almacén de datos es accesible públicamente). Si no se especifica, se usará Azure Integration Runtime. |Sin  |

**Ejemplo:**

```json
{
    "name": "ImpalaLinkedService",
    "properties": {
        "type": "Impala",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "authenticationType" : "UsernameAndPassword",
            "username" : "<username>",
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

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). En esta sección se proporciona una lista de las propiedades compatibles con el conjunto de datos de Impala.

Para copiar datos de Impala, establezca la propiedad type del conjunto de datos en **ImpalaObject**. No hay ninguna propiedad específica de tipo adicional en este tipo de conjunto de datos.

**Ejemplo**

```json
{
    "name": "ImpalaDataset",
    "properties": {
        "type": "ImpalaObject",
        "linkedServiceName": {
            "referenceName": "<Impala linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades compatibles con el tipo de origen de Impala.

### <a name="impala-as-a-source-type"></a>Impala como tipo de origen

Para copiar datos de Impala, establezca el tipo de origen de la actividad de copia en **ImpalaSource**. En la sección **source** de la actividad de copia se admiten las siguientes propiedades.

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del origen de la actividad de copia debe establecerse en **ImpalaSource**. | Sí |
| query | Use la consulta SQL personalizada para leer los datos. Un ejemplo es `"SELECT * FROM MyTable"`. | Sí |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromImpala",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Impala input dataset name>",
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
                "type": "ImpalaSource",
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
Para ver la lista de almacenes de datos que la actividad de copia de Data Factory admite como orígenes y receptores consulte [Almacenes de datos y formatos que se admiten](copy-activity-overview.md#supported-data-stores-and-formats).
