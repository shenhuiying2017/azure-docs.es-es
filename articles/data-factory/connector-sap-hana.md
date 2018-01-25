---
title: Copia de datos desde SAP HANA mediante Azure Data Factory | Microsoft Docs
description: "Obtenga información sobre cómo copiar datos desde SAP HANA en almacenes de datos receptores compatibles a través de una actividad de copia de una canalización de Azure Data Factory."
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
ms.openlocfilehash: cb70b6fee5257a07dda673d6d0f6feb07ad66958
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Copia de datos de SAP HANA mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-sap-hana-connector.md)
> * [Versión 2: versión preliminar](connector-sap-hana.md)

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos desde una base de datos SAP HANA. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte el artículo sobre [la versión 1 del conector SAP HANA](v1/data-factory-sap-hana-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos desde una base de datos SAP HANA a cualquier almacén de datos receptor compatible. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector SAP HANA admite las siguientes funcionalidades:

- Copiar datos de cualquier versión de la base de datos SAP HANA.
- Copiar de datos de **modelos de información de HANA** (como las vistas de análisis y cálculo) y las **tablas de fila o columna** mediante consultas SQL.
- La copia de datos con autenticación **básica** o de **Windows**.

> [!NOTE]
> Para copiar datos **en** un almacén de datos SAP HANA, use un conector ODBC genérico. Consulte el artículo sobre el [receptor de SAP HANA](connector-odbc.md#sap-hana-sink) con detalles. Tenga en cuenta que los servicios vinculados para el conector SAP HANA y el conector ODBC tienen tipos distintos y, por tanto, no se pueden reutilizar.

## <a name="prerequisites"></a>requisitos previos

Para usar este conector SAP HANA, necesitará lo siguiente:

- Configurar un entorno Integration Runtime autohospedado. Consulte el artículo sobre [Integration Runtime autohospedado](create-self-hosted-integration-runtime.md) para más información.
- Instale el controlador ODBC de SAP HANA en la máquina de Integration Runtime. Puede descargar el controlador ODBC de SAP HANA desde el [centro de descarga de software de SAP](https://support.sap.com/swdc). Busque con la palabra clave **SAP HANA CLIENT for Windows** (Cliente SAP HANA para Windows).

## <a name="getting-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Las secciones siguientes proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector SAP HANA.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado SAP HANA:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en: **SapHana** | Sí |
| Servidor | Nombre del servidor en el que reside la instancia de SAP HANA. Si el servidor usa un puerto personalizado, especifique `server:port`. | Sí |
| authenticationType | Tipo de autenticación usado para conectarse a la base de datos SAP HANA.<br/>Los valores permitidos son: **Básica** y **Windows** | Sí |
| userName | Nombre del usuario que tiene acceso al servidor SAP. | Sí |
| contraseña | Contraseña del usuario. Marque este campo como SecureString. | Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Tal y como se mencionó en los [requisitos previos](#prerequisites), se requiere un entorno Integration Runtime autohospedado. |Sí |

**Ejemplo:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
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

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre conjuntos de datos. En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de SAP HANA.

Para copiar datos desde SAP HANA, establezca la propiedad type del conjunto de datos en **RelationalTable**. No hay ninguna propiedad específica del tipo compatible con el conjunto de datos de SAP HANA de tipo RelationalTable.

**Ejemplo:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admite el origen de SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA como origen

Para copiar datos desde SAP HANA, establezca el tipo de origen de la actividad de copia como **RelationalSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del origen de la actividad de copia debe establecerse en: **RelationalSource** | Sí |
| query | Especifica la consulta SQL para leer datos de la instancia de SAP HANA. | Sí |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-hana"></a>Asignación de tipos de datos para SAP HANA

Al copiar datos desde SAP HANA, se usan las siguientes asignaciones de tipos de datos de SAP HANA en los tipos de datos provisionales de Azure Data Factory. Consulte el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen al receptor.

| Tipo de datos de SAP HANA | Tipo de datos provisionales de Data Factory |
|:--- |:--- |
| ALPHANUM | string |
| BIGINT | Int64 |
| BLOB | Byte[] |
| BOOLEAN | Byte |
| CLOB | Byte[] |
| DATE | Datetime |
| DECIMAL | DECIMAL |
| DOUBLE | Single |
| INT | Int32 |
| NVARCHAR | string |
| REAL | Single |
| SECONDDATE | Datetime |
| SMALLINT | Int16 |
| TIME | timespan |
| TIMESTAMP | Datetime |
| TINYINT | Byte |
| VARCHAR | string |

## <a name="known-limitations"></a>Limitaciones conocidas

Cuando se copian datos de SAP HANA, hay algunas limitaciones conocidas:

- Las cadenas NVARCHAR se truncan al llegar a la longitud máxima de 4000 caracteres Unicode
- SMALLDECIMAL no se admite
- VARBINARY no se admite
- Las fechas válidas son las del intervalo entre 30/12/1899 y 31/12/9999


## <a name="next-steps"></a>pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.