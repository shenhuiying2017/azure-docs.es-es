---
title: Copia de datos desde Salesforce mediante Azure Data Factory | Microsoft Docs
description: "Obtenga información sobre cómo copiar datos desde Salesforce en almacenes de datos receptores compatibles a través de una actividad de copia de una canalización de Azure Data Factory."
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
ms.date: 08/30/2017
ms.author: jingwang
ms.openlocfilehash: 7978e955bf5516a853443555ab10a69dcf22d63f
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-salesforce-using-azure-data-factory"></a>Copia de datos desde Salesforce mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: disponibilidad general](v1/data-factory-salesforce-connector.md)
> * [Versión 2: versión preliminar](connector-salesforce.md)

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos desde una base de datos Salesforce. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en la versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte el artículo sobre [la versión 1 del conector Salesforce](v1/data-factory-salesforce-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos desde una base de datos Salesforce a cualquier almacén de datos receptor compatible. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector es compatible con las siguientes ediciones de Salesforce: **Developer Edition, Professional Edition, Enterprise Edition o Unlimited Edition**. Y admite la copia del **dominio personalizado, producción y espacio aislado** de Salesforce.

## <a name="prerequisites"></a>Requisitos previos

* El permiso API debe estar habilitado en Salesforce. Consulte [How do I enable API access in Salesforce by permission set?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Límites de solicitudes de Salesforce

Salesforce tiene límites para el número total de solicitudes de API y el de solicitudes de API simultáneas. Tenga en cuenta los siguientes puntos:

- Si el número de solicitudes simultáneas supera el límite, se produce la limitación y verá errores aleatorios.
- Si el número total de solicitudes supera el límite, la cuenta de Salesforce se bloqueará durante 24 horas.

También podría recibir el error "REQUEST_LIMIT_EXCEEDED" en ambos escenarios. Consulte la sección API Request Limits (Límites de solicitudes de API) del artículo [Salesforce Developer Limits](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) (Límites de desarrollador de Salesforce) para más información.

## <a name="getting-started"></a>Introducción
Puede crear una canalización con la actividad de copia mediante el SDK de .NET, el SDK de Python, Azure PowerShell, la API de REST o la plantilla de Azure Resource Manager. Consulte el [tutorial de actividad de copia](quickstart-create-data-factory-dot-net.md) para obtener instrucciones paso a paso sobre cómo crear una canalización con una actividad de copia.

Las secciones siguientes proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas para el conector Salesforce.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado Salesforce:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type |La propiedad type debe establecerse en: **Salesforce**. |Sí |
| environmentUrl | Especifique la URL de la instancia de Salesforce. <br><br> - El valor predeterminado es `"https://login.salesforce.com"`. <br> - Para copiar datos desde el espacio aislado, especifique `"https://test.salesforce.com"`. <br> - Para copiar datos del dominio personalizado, especifique, por ejemplo, `"https://[domain].my.salesforce.com"`. |No |
| nombre de usuario |Especifique el nombre de usuario de la cuenta de usuario. |Sí |
| contraseña |Especifique la contraseña para la cuenta de usuario. |Sí |
| securityToken |Especifique el token de seguridad para la cuenta de usuario. Consulte [Get security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Obtención de un token de seguridad) para ver instrucciones sobre cómo restablecer u obtener un token de seguridad. Para más información acerca de los tokens de seguridad en general, consulte [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)(Seguridad y la API). |Sí |

**Ejemplo:**

```json
{
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        }
    },
    "name": "SalesforceLinkedService"
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre conjuntos de datos. En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de Salesforce.

Para copiar datos desde Salesforce, establezca la propiedad type del conjunto de datos en **RelationalTable**. Se admiten las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos debe establecerse en: **RelationalTable** | Sí |
| tableName | Nombre de la tabla de la base de datos Salesforce. | No (si se especifica "query" en el origen de la actividad) |

> [!IMPORTANT]
> La parte "__c" del nombre de la API es necesaria para cualquier objeto personalizado.

![Data Factory - Conexión a Salesforce - Nombre de la API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Ejemplo:**

```json
{
    "name": "SalesforceDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable__c"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admite el origen de Salesforce.

### <a name="salesforce-as-source"></a>Salesforce como origen

Para copiar datos desde Salesforce, establezca el tipo de origen de la actividad de copia como **RelationalSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en: **RelationalSource** | Sí |
| query |Utilice la consulta personalizada para leer los datos. Puede usar una consulta de SQL-92 o de [Salesforce Object Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Por ejemplo: `select * from MyTable__c`. | No (si se especifica "tableName" en el conjunto de datos) |

> [!IMPORTANT]
> La parte "__c" del nombre de la API es necesaria para cualquier objeto personalizado.

![Data Factory - Conexión a Salesforce - Nombre de la API](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="query-tips"></a>Sugerencias de consulta

### <a name="retrieving-data-from-salesforce-report"></a>Recuperación de datos de informes de Salesforce

Puede recuperar datos de informes de Salesforce especificando la consulta como `{call "<report name>"}. Example: `"query": "{call \"TestReport\"}".

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Recuperación de los registros eliminados desde la papelera de reciclaje de Salesforce

Para consultar los registros eliminados temporalmente de papelera de reciclaje de Salesforce, puede especificar **"IsDeleted = 1"** en la consulta. Por ejemplo,

* Para consultar solo los registros eliminados, especifique "select * from MyTable__c **where IsDeleted= 1**"
* Para consultar todos los registros, tanto existentes como eliminados, especifique "select * from MyTable__c **where IsDeleted = 0 or IsDeleted = 1**"

### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Recuperación de datos mediante la cláusula WHERE en la columna DateTime

Cuando se especifica la consulta SQL o SOQL, preste atención a la diferencia del formato de fecha y hora. Por ejemplo:

* **Ejemplo SOQL**:`$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', <datetime parameter>, <datetime parameter>)`
* **Ejemplo de SQL**: `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', <datetime parameter>, <datetime parameter>)`

## <a name="data-type-mapping-for-salesforce"></a>Asignación de tipos de datos para Salesforce

Al copiar datos desde Salesforce, se usan las siguientes asignaciones de tipos de datos de Salesforce en los tipos de datos provisionales de Azure Data Factory. Consulte el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen al receptor.

| Tipos de datos de Salesforce | Tipo de datos provisionales de Data Factory |
|:--- |:--- |
| Numeración automática |String |
| Casilla de verificación |Booleano |
| Moneda |Doble |
| Date |DateTime |
| Fecha y hora |DateTime |
| Email |String |
| Id |String |
| Relación de búsqueda |String |
| Lista desplegable de selección múltiple |String |
| Number |Doble |
| Percent |Doble |
| Teléfono |String |
| Lista desplegable |String |
| Texto |String |
| Área de texto |String |
| Área de texto (largo) |String |
| Área de texto (enriquecido) |String |
| Texto (cifrado) |String |
| URL |String |


## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md##supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.