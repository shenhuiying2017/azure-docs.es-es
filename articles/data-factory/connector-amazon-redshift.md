---
title: Copia de datos de Amazon Redshift mediante Azure Data Factory | Microsoft Docs
description: "Obtenga información sobre cómo copiar datos desde Amazon Redshift a almacenes de datos de receptor compatibles mediante Azure Data Factory."
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
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: 1e1c8e03bbfc2a07f4d4faee4c3b171c44fa312d
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Copia de datos de Amazon Redshift mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-amazon-redshift-connector.md)
> * [Versión 2: versión preliminar](connector-amazon-redshift.md)


En este artículo se resume el uso de la actividad de copia de Azure Data Factory para copiar datos de Amazon Redshift. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general (GA), vea el artículo sobre la [versión 1 del conector de Amazon Redshift](v1/data-factory-amazon-redshift-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos de Amazon Redshift en cualquier almacén de datos de receptor admitido. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector de Amazon Redshift admite la recuperación de datos de Redshift mediante una consulta o la compatibilidad integrada con Redshift UNLOAD.

> [!TIP]
> Para obtener el mejor rendimiento al copiar grandes cantidades de datos desde Redshift, considere usar Redshift UNLOAD integrado a través de Amazon S3. Vea la sección [Uso de UNLOAD para copiar datos de Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift) para más detalles.

## <a name="prerequisites"></a>requisitos previos

* Si va a copiar datos a un local almacén de datos local mediante [Integration Runtime autohospedado](create-self-hosted-integration-runtime.md), conceda a Integration Runtime (use la dirección IP de la máquina) el acceso al clúster de Amazon Redshift. Consulte [Authorize access to the cluster](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) (Autorización para acceder al clúster) para obtener instrucciones.
* Si va a copiar datos a un almacén de datos de Azure, consulte [Azure Data Center IP Ranges](https://www.microsoft.com/download/details.aspx?id=41653) (Intervalos de direcciones IP de Azure Data Center) para ver los intervalos de direcciones IP de Compute y los intervalos SQL que se utilizan en los centros de datos de Azure.

## <a name="getting-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector de Amazon Redshift.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado Amazon Redshift:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en: **AmazonRedshift**. | Sí |
| Servidor |Dirección IP o nombre de host del servidor de Amazon Redshift. |Sí |
| puerto |El número del puerto TCP que el servidor de Amazon Redshift utiliza para escuchar las conexiones del cliente. |No, el valor predeterminado es 5439 |
| Base de datos |Nombre de la base de datos de Amazon Redshift. |Sí |
| Nombre de usuario |Nombre del usuario que tiene acceso a la base de datos. |Sí |
| contraseña |Contraseña para la cuenta de usuario. Marque este campo como SecureString. |Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) (si el almacén de datos se encuentra en una red privada) o Azure Integration Runtime. Si no se especifica, se usará Azure Integration Runtime. |Sin  |

**Ejemplo:**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "<server name>",
            "database": "<database name>",
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

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre conjuntos de datos. En esta sección se proporciona una lista de las propiedades que el conjunto de datos de Amazon Redshift admite.

Para copiar datos desde Amazon Redshift, establezca la propiedad type del conjunto de datos en **RelationalTable**. Se admiten las siguientes propiedades:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del conjunto de datos debe establecerse en: **RelationalTable** | Sí |
| tableName | Nombre de la tabla en Amazon Redshift. | No (si se especifica "query" en el origen de la actividad) |

**Ejemplo**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que el origen Amazon Redshift admite.

### <a name="amazon-redshift-as-source"></a>Amazon Redshift como origen

Para copiar datos desde Amazon Redshift, establezca el tipo de origen de la actividad de copia en **AmazonRedshiftSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del origen de la actividad de copia debe establecerse en: **AmazonRedshiftSource** | Sí |
| query |Utilice la consulta personalizada para leer los datos. |Cadena de consulta SQL. Por ejemplo: select * from MyTable. |No (si se especifica "tableName" en el conjunto de datos) |
| redshiftUnloadSettings | Grupo de propiedades al usar Amazon Redshift UNLOAD. | Sin  |
| s3LinkedServiceName | Hace referencia a Amazon S3 que se usa como almacenamiento provisional especificando para ello un nombre de servicio vinculado de tipo "AmazonS3". | Sí, se utiliza UNLOAD |
| bucketName | Indique el depósito S3 para almacenar los datos provisionales. Si no se proporciona, el servicio Data Factory lo genera automáticamente.  | Sí, se utiliza UNLOAD |

**Ejemplo: Origen Amazon Redshift de la actividad de copia mediante UNLOAD**

```json
"source": {
    "type": "AmazonRedshiftSource",
    "query": "<SQL query>",
    "redshiftUnloadSettings": {
        "s3LinkedServiceName": {
            "referenceName": "<Amazon S3 linked service>",
            "type": "LinkedServiceReference"
        },
        "bucketName": "bucketForUnload"
    }
}
```

Obtenga más información en la sección siguiente sobre cómo usar UNLOAD para copiar datos desde Amazon Redshift de forma eficaz.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Uso de UNLOAD para copiar datos de Amazon Redshift

[UNLOAD](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) es un mecanismo proporcionado por Amazon Redshift, que puede descargar los resultados de una consulta a uno o varios archivos en Amazon Simple Storage Service (Amazon S3). Es la manera que Amazon recomienda para copiar el conjunto de datos grande de Redshift.

**Ejemplo: copiar datos desde Amazon Redshift en Azure SQL Data Warehouse con UNLOAD, copia preconfigurada y PolyBase**

En este caso de uso de ejemplo, la actividad de copia descarga los datos de Amazon Redshift en Amazon S3 según se configura en "redshiftUnloadSettings", luego copia los datos de Amazon S3 en Azure Blob, según se especifica en "stagingSettings", y al final se usa PolyBase para cargar los datos en SQL Data Warehouse. Todo el formato provisional es controlado como corresponde por la actividad de copia.

![Redshift al flujo de trabajo de copia de almacenamiento de datos de SQL](media\copy-data-from-amazon-redshift\redshift-to-sql-dw-copy-workflow.png)

```json
"activities":[
    {
        "name": "CopyFromAmazonRedshiftToSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "AmazonRedshiftDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRedshiftSource",
                "query": "select * from MyTable",
                "redshiftUnloadSettings": {
                    "s3LinkedServiceName": {
                        "referenceName": "AmazonS3LinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "bucketName": "bucketForUnload"
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "AzureStorageLinkedService",
                "path": "adfstagingcopydata"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Asignación de tipos de datos para Amazon Redshift

Al copiar datos desde Amazon Redshift, se utilizan las siguientes asignaciones de tipos de datos de Amazon Redshift en los tipos de datos provisionales de Azure Data Factory. Consulte el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen al receptor.

| Tipo de datos de Amazon Redshift | Tipo de datos provisionales de Data Factory |
|:--- |:--- |
| BIGINT |Int64 |
| BOOLEAN |string |
| CHAR |string |
| DATE |Datetime |
| DECIMAL |DECIMAL |
| DOUBLE PRECISION |Doble |
| INTEGER |Int32 |
| REAL |Single |
| SMALLINT |Int16 |
| TEXT |string |
| TIMESTAMP |Datetime |
| VARCHAR |string |

## <a name="next-steps"></a>pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md##supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.
