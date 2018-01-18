---
title: Copia de datos desde y hacia Salesforce mediante Azure Data Factory | Microsoft Docs
description: "Obtenga información sobre cómo copiar datos desde Salesforce a almacenes de datos receptores compatibles, o bien desde almacenes de datos de origen compatibles a Salesforce a través de una actividad de copia de una canalización de Azure Data Factory."
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
ms.openlocfilehash: 7cd86922b0445fc81766ca54080e2fd3e64a6c61
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-fromto-salesforce-using-azure-data-factory"></a>Copia de datos desde y hacia Salesforce mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-salesforce-connector.md)
> * [Versión 2: versión preliminar](connector-salesforce.md)

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos con Salesforce como origen o destino. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte el artículo sobre [la versión 1 del conector Salesforce](v1/data-factory-salesforce-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos desde Salesforce a cualquier almacén de datos receptor, o copiar datos desde cualquier almacén de datos de origen a Salesforce. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector de Salesforce admite:

- Las siguientes ediciones de Salesforce: **Developer Edition, Professional Edition, Enterprise Edition o Unlimited Edition**.
- La copia de datos desde y hacia **producción, espacio aislado y dominio personalizado** de Salesforce.

## <a name="prerequisites"></a>Requisitos previos

* El permiso API debe estar habilitado en Salesforce. Consulte [How do I enable API access in Salesforce by permission set?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Límites de solicitudes de Salesforce

Salesforce tiene límites para el número total de solicitudes de API y el de solicitudes de API simultáneas. Tenga en cuenta los siguientes puntos:

- Si el número de solicitudes simultáneas supera el límite, se produce la limitación y verá errores aleatorios.
- Si el número total de solicitudes supera el límite, la cuenta de Salesforce se bloqueará durante 24 horas.

También podría recibir el error "REQUEST_LIMIT_EXCEEDED" en ambos escenarios. Consulte la sección API Request Limits (Límites de solicitudes de API) del artículo [Salesforce Developer Limits](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) (Límites de desarrollador de Salesforce) para más información.

## <a name="getting-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Las secciones siguientes proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas para el conector Salesforce.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado Salesforce:

| Propiedad | DESCRIPCIÓN | Requerido |
|:--- |:--- |:--- |
| Tipo |La propiedad type debe establecerse en: **Salesforce**. |Sí |
| environmentUrl | Especifique la URL de la instancia de Salesforce. <br> - El valor predeterminado es `"https://login.salesforce.com"`. <br> - Para copiar datos desde el espacio aislado, especifique `"https://test.salesforce.com"`. <br> - Para copiar datos del dominio personalizado, especifique, por ejemplo, `"https://[domain].my.salesforce.com"`. |Sin  |
| Nombre de usuario |Especifique el nombre de usuario de la cuenta de usuario. |Sí |
| contraseña |Especifique la contraseña para la cuenta de usuario.<br/><br/>Puede seleccionar este campo como SecureString para almacenarlo de forma segura en ADF, o almacenar la contraseña en Azure Key Vault y permitir que se copie la extracción de la actividad desde allí al realizar la copia de datos. Obtenga más información sobre el [Almacenamiento de credenciales en Key Vault](store-credentials-in-key-vault.md). |Sí |
| securityToken |Especifique el token de seguridad para la cuenta de usuario. Consulte [Get security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Obtención de un token de seguridad) para ver instrucciones sobre cómo restablecer u obtener un token de seguridad. Para más información acerca de los tokens de seguridad en general, consulte [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)(Seguridad y la API).<br/><br/>Puede seleccionar este campo como SecureString para almacenarlo de forma segura en ADF, o almacenar el token de seguridad en Azure Key Vault y permitir que se copie la extracción de la actividad desde allí al realizar la copia de datos. Obtenga más información sobre el [Almacenamiento de credenciales en Key Vault](store-credentials-in-key-vault.md). |Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Si no se especifica, se usará Azure Integration Runtime. | "No" para el origen, "Sí" para el receptor si el servicio vinculado al origen no tiene ningún entorno de ejecución de integración. |

>[!IMPORTANT]
>Al copiar datos **a** Salesforce, no podrá utilizarse el entorno de Azure Integration Runtime predeterminado para ejecutar la copia. En otras palabras, si el servicio vinculado al origen no tiene ningún entorno de ejecución de integración especificado, [cree una instancia de Azure IR](create-azure-integration-runtime.md#create-azure-ir) explícitamente con una ubicación próxima a Salesforce y asocie el servicio vinculado a Salesforce como en el ejemplo siguiente.

**Ejemplo: almacenamiento de credenciales en ADF**

```json
{
    "name": "SalesforceLinkedService",
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
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Ejemplo: almacenamiento de credenciales en Azure Key Vault**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de Salesforce.

Para copiar datos desde y hacia Salesforce, establezca la propiedad type del conjunto de datos en **SalesforceObject**. Se admiten las siguientes propiedades:

| Propiedad | DESCRIPCIÓN | Requerido |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en: **SalesforceObject**.  | Sí |
| objectApiName | El nombre del objeto de Salesforce desde el que se van a recuperar los datos. | No para el origen, sí para el receptor |

> [!IMPORTANT]
> La parte "__c" del nombre de la API es necesaria para cualquier objeto personalizado.

![Data Factory - Conexión a Salesforce - Nombre de la API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Ejemplo:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "objectApiName": "MyTable__c"
        }
    }
}
```

>[!NOTE]
>Para la compatibilidad con versiones anteriores, cuando se copian datos de Salesforce, se podrá seguir usando el conjunto de datos de tipo "RelationalTable" anterior, pero se sugiere cambiar al nuevo tipo "SalesforceObject".

| Propiedad | DESCRIPCIÓN | Requerido |
|:--- |:--- |:--- |
| Tipo | La propiedad type del conjunto de datos debe establecerse en: **RelationalTable** | Sí |
| tableName | Nombre de la tabla de Salesforce. | No (si se especifica "query" en el origen de la actividad) |

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades admitidas por el origen y el receptor de Salesforce.

### <a name="salesforce-as-source"></a>Salesforce como origen

Para copiar datos desde Salesforce, establezca el tipo de origen de la actividad de copia en **SalesforceSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Requerido |
|:--- |:--- |:--- |
| Tipo | La propiedad type del origen de la actividad de copia debe establecerse en: **SalesforceSource** | Sí |
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
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>Para la compatibilidad con versiones anteriores, cuando se copian datos de Salesforce, se podrá seguir usando el origen de copia de tipo "RelationalSource" anterior, pero se sugiere cambiar al nuevo tipo "SalesforceSource".

### <a name="salesforce-as-sink"></a>Salesforce como receptor

Para copiar datos hacia Salesforce, establezca el tipo de receptor de la actividad de copia en **SalesforceSink**. Se admiten las siguientes propiedades en la sección **sink** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Requerido |
|:--- |:--- |:--- |
| Tipo | La propiedad type del receptor de la actividad de copia debe establecerse en: **SalesforceSink**. | Sí |
| writeBehavior | El comportamiento de escritura de la operación.<br/>Los valores permitidos son: **Insert** y **Upsert**. | No (el valor predeterminado es Insert) |
| externalIdFieldName | El nombre del campo de identificador externo para la operación de upsert. El campo especificado debe definirse como "Campo de identificador externo" en el objeto de Salesforce y no puede tener valores NULL en los datos de entrada correspondientes. | Sí para "Upsert" |
| writeBatchSize | El recuento de filas de datos escritos en Salesforce en cada lote. | No (el valor predeterminado es 5000) |
| ignoreNullValues | Indica si se omiten los valores nulos de los datos de entrada durante la operación de escritura.<br/>Los valores permitidos son **true** y **false**.<br>- **true**: deje invariables los datos en el objeto de destino al realizar la operación de actualización/upsert, e inserte el valor predeterminado definido al realizar la operación de inserción.<br/>- **false**: actualice los datos en el objeto de destino a NULL al realizar la operación de actualización/upsert, e inserte el valor NULL al realizar la operación de inserción. | No (el valor predeterminado es false) |

### <a name="example-salesforce-sink-in-copy-activity"></a>Ejemplo: receptor de Salesforce en la actividad de copia

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Sugerencias de consulta

### <a name="retrieving-data-from-salesforce-report"></a>Recuperación de datos de informes de Salesforce

Puede recuperar datos de informes de Salesforce especificando las consultas como `{call "<report name>"}`. Ejemplo: `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Recuperación de los registros eliminados desde la papelera de reciclaje de Salesforce

Para consultar los registros eliminados temporalmente de papelera de reciclaje de Salesforce, puede especificar **"IsDeleted = 1"** en la consulta. Por ejemplo,

* Para consultar solo los registros eliminados, especifique "select * from MyTable__c **where IsDeleted= 1**"
* Para consultar todos los registros, tanto existentes como eliminados, especifique "select * from MyTable__c **where IsDeleted = 0 or IsDeleted = 1**"

### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Recuperación de datos mediante la cláusula WHERE en la columna DateTime

Cuando se especifica la consulta SQL o SOQL, preste atención a la diferencia del formato de fecha y hora. Por ejemplo: 

* **Ejemplo SOQL**:`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Ejemplo de SQL**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}"`

## <a name="data-type-mapping-for-salesforce"></a>Asignación de tipos de datos para Salesforce

Al copiar datos desde Salesforce, se usan las siguientes asignaciones de tipos de datos de Salesforce en los tipos de datos provisionales de Azure Data Factory. Consulte el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen al receptor.

| Tipos de datos de Salesforce | Tipo de datos provisionales de Data Factory |
|:--- |:--- |
| Numeración automática |string |
| Casilla de verificación |boolean |
| Moneda |Doble |
| Date |Datetime |
| Fecha y hora |Datetime |
| Email |string |
| Id |string |
| Relación de búsqueda |string |
| Lista desplegable de selección múltiple |string |
| Number |Doble |
| Percent |Doble |
| Teléfono |string |
| Lista desplegable |string |
| Texto |string |
| Área de texto |string |
| Área de texto (largo) |string |
| Área de texto (enriquecido) |string |
| Texto (cifrado) |string |
| URL |string |

## <a name="next-steps"></a>pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.