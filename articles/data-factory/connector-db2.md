---
title: Copia de datos desde DB2 mediante Azure Data Factory | Microsoft Docs
description: "Obtenga información sobre cómo copiar datos desde DB2 a almacenes de datos receptores compatibles a través de una actividad de copia de una canalización de Azure Data Factory."
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
ms.date: 
ms.author: jingwang
ms.openlocfilehash: a96de1814afc7947205a0dc7ed005f7cadff20bc
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Copia de datos desde DB2 mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-onprem-db2-connector.md)
> * [Versión 2: Versión preliminar](connector-db2.md)

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos desde una base de datos DB2. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea el artículo sobre [la versión 1 del conector DB2](v1/data-factory-onprem-db2-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos desde la base de datos DB2 a cualquier almacén de datos receptor compatible. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector DB2 admite las siguientes plataformas y versiones de IBM DB2 con las versiones 9, 10 y 11 de SQL Access Manager (SQLAM) de la arquitectura distribuida de bases de datos relacionales (DRDA):

* IBM DB2 para z/OS 11.1
* IBM DB2 para z/OS 10.1
* IBM DB2 para i 7.2
* IBM DB2 para i 7.1
* IBM DB2 para LUW 11
* IBM DB2 para LUW 10.5
* IBM DB2 para LUW 10.1

> [!TIP]
> Si recibe un mensaje de error que indica "No se encontró el paquete correspondiente a una solicitud de ejecución de la instrucción SQL. SQLSTATE=51002 SQLCODE=-805", es porque no se creó un paquete necesario para el usuario normal en ese SO. Siga estas instrucciones según el tipo de servidor DB2:
> - DB2 para i (AS400): permita que el usuario avanzado cree la colección del usuario de inicio de sesión antes de usar la actividad de copia. Comando: `create collection <username>`
> - DB2 para z/OS o LUW: use una cuenta con privilegios elevados (usuario avanzado o administrador con entidades de paquete y permisos BIND, BINDADD y GRANT EXECUTE TO PUBLIC) para ejecutar una vez la actividad de copia y, luego, el paquete necesario se creará automáticamente durante la copia. A continuación, puede cambiar al usuario normal para las ejecuciones de copia posteriores.

## <a name="prerequisites"></a>requisitos previos

Para usar los datos de la copia de una base de datos DB2 que no es accesible públicamente, debe configurar un entorno Integration Runtime (autohospedado). Para obtener información sobre los entornos Integration Runtime (autohospedado), consulte el artículo sobre los [entornos Integration Runtime (autohospedado)](create-self-hosted-integration-runtime.md). El entorno Integration Runtime proporciona un controlador DB2 integrado, por lo tanto, no es necesario instalar manualmente los controladores cuando se copian datos desde DB2.

## <a name="getting-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector DB2.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de DB2:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en: **Db2**. | Sí |
| Servidor |Nombre del servidor DB2. |Sí |
| Base de datos |Nombre de la base de datos DB2. |Sí |
| schema |Nombre del esquema de la base de datos. El nombre del esquema distingue mayúsculas de minúsculas. |Sin  |
| authenticationType |Tipo de autenticación usado para conectarse a la base de datos DB2.<br/>El valor permitido es **Básica**. |Sí |
| Nombre de usuario |Especifique el nombre de usuario para conectarse a la base de datos DB2. |Sí |
| contraseña |Especifique la contraseña de la cuenta de usuario especificada para el nombre de usuario. Marque este campo como SecureString. |Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) o Azure Integration Runtime (si el almacén de datos es accesible públicamente). Si no se especifica, se usará Azure Integration Runtime. |Sin  |

**Ejemplo:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername>",
            "database": "<dbname>",
            "authenticationType": "Basic",
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

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre conjuntos de datos. En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de DB2.

Para copiar datos desde DB2, establezca la propiedad type del conjunto de datos en **RelationalTable**. Se admiten las siguientes propiedades:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del conjunto de datos debe establecerse en: **RelationalTable** | Sí |
| tableName | Nombre de la tabla de la base de datos DB2. | No (si se especifica "query" en el origen de la actividad) |

**Ejemplo**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admite el origen de DB2.

### <a name="db2-as-source"></a>DB2 como origen

Para copiar datos desde DB2, establezca el tipo de origen de la actividad de copia como **RelationalSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del origen de la actividad de copia debe establecerse en: **RelationalSource** | Sí |
| query | Use la consulta SQL personalizada para leer los datos. Por ejemplo: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""`. | No (si se especifica "tableName" en el conjunto de datos) |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-db2"></a>Asignación de tipos de datos de DB2

Al copiar datos desde DB2, se utilizan las siguientes asignaciones de tipos de datos de DB2 en los tipos de datos provisionales de Azure Data Factory. Consulte el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen al receptor.

| Tipo de base de datos DB2 | Tipo de datos provisionales de Data Factory |
|:--- |:--- |
| BigInt |Int64 |
| Binary |Byte[] |
| Blob |Byte[] |
| Char |string |
| Clob |string |
| Date |DateTime |
| DB2DynArray |string |
| DbClob |string |
| DECIMAL |DECIMAL |
| DecimalFloat |DECIMAL |
| Doble |Doble |
| Float |Doble |
| Graphic |string |
| Entero |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |string |
| LongVarGraphic |string |
| Numeric |DECIMAL |
| Real |Single |
| SmallInt |Int16 |
| Hora |timespan |
| Timestamp |Datetime |
| VarBinary |Byte[] |
| VarChar |string |
| VarGraphic |string |
| xml |Byte[] |


## <a name="next-steps"></a>pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md##supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.