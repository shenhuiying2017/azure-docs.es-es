---
title: Copia de datos desde y hacia Dynamics CRM o Dynamics 365 mediante Azure Data Factory | Microsoft Docs
description: "Aprenda a copiar datos desde Microsoft Dynamics CRM o Microsoft Dynamics 365, a almacenes de datos receptores compatibles, o bien desde almacenes de datos de origen compatibles a Dynamics CRM o Dynamics 365 a través de una actividad de copia en una canalización de Azure Data Factory."
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
ms.openlocfilehash: b00c594f87a3126bd3f1548cd904adffcb214031
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="copy-data-from-and-to-dynamics-365-or-dynamics-crm-by-using-azure-data-factory"></a>Copia de datos desde y hacia Dynamics 365 o Dynamics CRM mediante Azure Data Factory

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos desde y hacia Microsoft Dynamics 365 o Microsoft Dynamics CRM. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de esta actividad.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 de Data Factory, que está disponible con carácter general, consulte el artículo sobre la [actividad de copia de la versión 1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos desde Dynamics 365 o Dynamics CRM a cualquier almacén de datos receptor compatible. También puede copiar datos desde cualquier almacén de datos de origen no compatible a Dynamics 365 o Dynamics CRM. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

Este conector de Dynamics admite los siguientes tipos de autenticación y versiones de Dynamics. IFD es la abreviatura en inglés del término "implementación con conexión a Internet".

| Versiones de Dynamics | Tipos de autenticación | Ejemplos de servicios vinculados |
|:--- |:--- |:--- |
| Dynamics 365 Online <br> Dynamics CRM Online | Office 365 | [Autenticación de Dynamics Online + Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 local con IFD <br> Dynamics CRM 2016 local con IFD <br> Dynamics CRM 2015 local con IFD | IFD | [Dynamics local con autenticación de IFD + IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Para Dynamics 365 en concreto, se admiten los siguientes tipos de aplicación:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Otros tipos de aplicaciones como Operations and Finance, Talent, etc, no son compatibles.

## <a name="get-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas de Dynamics.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de Dynamics.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 y Dynamics CRM Online

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en: **Dynamics**. | Sí |
| deploymentType | El tipo de implementación de la instancia de Dynamics. Debe ser **"Online"** para Dynamics Online. | Sí |
| organizationName | El nombre de la organización de la instancia de Dynamics. | No, se debe especificar cuando hay más de una instancia de Dynamics asociada al usuario |
| authenticationType | Tipo de autenticación para conectarse a un servidor de Dynamics. Especifique **"Office365"** para Dynamics Online. | Sí |
| nombre de usuario | Especifique el nombre de usuario para conectarse a Dynamics. | Sí |
| contraseña | Especifique la contraseña de la cuenta de usuario que especificó para el nombre de usuario. Marque este campo como SecureString para almacenarlo de forma segura en Data Factory o [para hacer referencia a un secreto almacenado en Azure Key Vault](store-credentials-in-key-vault.md). | Sí |
| connectVia | El [entorno de ejecución de integración](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Si no se especifica, se usará Azure Integration Runtime. | "No" para el origen, "Sí" para el receptor si el servicio vinculado al origen no tiene ningún entorno de ejecución de integración. |

>[!IMPORTANT]
>Al copiar datos a Dynamics, no podrá utilizarse el entorno de ejecución de integración predeterminado de Azure para ejecutar la copia. En otras palabras, si el servicio vinculado al origen no tiene ningún entorno de ejecución de integración, [cree explícitamente un entorno de ejecución de integración de Azure](create-azure-integration-runtime.md#create-azure-ir) con una ubicación cercana a la de la instancia de Dynamics. Asócielo al servicio vinculado de Dynamics como en el ejemplo siguiente.

**Ejemplo: Dynamics Online mediante la autenticación de Office 365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "organizationName": "orga02d9c75",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 y Dynamics CRM local con IFD

*Las propiedades adicionales, en comparación con Dynamics Online, son "hostName" y "port".*

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en: **Dynamics**. | Sí |
| deploymentType | El tipo de implementación de la instancia de Dynamics. Debe ser **"OnPremisesWithIfd"** para Dynamic local con IFD.| Sí |
| hostName | El nombre de host del servidor local de Dynamics. | Sí |
| puerto | El puerto del servidor local de Dynamics. | No (el valor predeterminado es 443) |
| organizationName | El nombre de la organización de la instancia de Dynamics. | Sí |
| authenticationType | Tipo de autenticación para conectarse al servidor de Dynamics. Especifique **"Ifd"** para Dynamics local con IFD. | Sí |
| nombre de usuario | Especifique el nombre de usuario para conectarse a Dynamics. | Sí |
| contraseña | Especifique la contraseña de la cuenta de usuario que especificó para el nombre de usuario. Puede elegir marcar este campo como SecureString para almacenarlo de forma segura en ADF o almacenar la contraseña en Azure Key Vault y permitir que la actividad de copia incorpore los cambios desde allí al realizar la copia de datos. Obtenga más información sobre el [Almacenamiento de credenciales en Key Vault](store-credentials-in-key-vault.md). | Sí |
| connectVia | El [entorno de ejecución de integración](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Si no se especifica, se usará Azure Integration Runtime. | No para el origen, sí para el receptor |

>[!IMPORTANT]
>Para copiar datos en Dynamics, [cree explícitamente un entorno de ejecución de integración de Azure](create-azure-integration-runtime.md#create-azure-ir) con una ubicación cercana a la de la instancia de Dynamics. Asócielo al servicio vinculado como en el ejemplo siguiente.

**Ejemplo: Dynamics local con IFD mediante la autenticación de IFD**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
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

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de Dynamics.

Para copiar datos desde y hacia Dynamics, establezca la propiedad type del conjunto de datos en **DynamicsEntity**. Se admiten las siguientes propiedades.

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del conjunto de datos debe establecerse en: **DynamicsEntity**. |Sí |
| entityName | El nombre lógico de la entidad que se va a recuperar. | No para el origen (si se especifica "query" en el origen de la actividad); sí para el receptor |

> [!IMPORTANT]
>- Cuando se copian datos desde Dynamics, se requiere la sección "structure" en el conjunto de datos de Dynamics. Esta define el nombre de columna y el tipo de datos de los datos de Dynamics que desea copiar. Para más información, consulte [Estructura del conjunto de datos](concepts-datasets-linked-services.md#dataset-structure) y [Asignación de tipos de datos de Dynamics](#data-type-mapping-for-dynamics).
>- Al copiar datos a Dynamics, la sección "structure" es opcional en el conjunto de datos de Dynamics. El esquema de datos de origen determinará qué columnas se copian. Si el origen es un archivo CSV sin encabezado, en el conjunto de datos de entrada, especifique "structure" con el tipo de datos y el nombre de columna. Estos se asignan a los campos del archivo CSV, uno por uno en orden.

**Ejemplo:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
        "typePoperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admite el tipo de origen y el tipo de receptor de Dynamics.

### <a name="dynamics-as-a-source-type"></a>Dynamics como tipo de origen

Para copiar datos de Dynamics, establezca el tipo de origen de la actividad de copia en **DynamicsSource**. En la sección **source** de la actividad de copia se admiten las siguientes propiedades.

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del origen de la actividad de copia debe establecerse en: **DynamicsSource**. | Sí |
| query | FetchXML es un lenguaje de consulta patentado que se usa en Dynamics (Online y local). Consulte el ejemplo siguiente. Para más información, consulte [Build queries with FetchXML](https://msdn.microsoft.com/en-us/library/gg328332.aspx) (Creación de consultas con FetchXML). | No (si se especifica "entityName" en el conjunto de datos) |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Consulta FetchXML de ejemplo

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-a-sink-type"></a>Dynamics como tipo de receptor

Para copiar datos en Dynamics, establezca el tipo de receptor de la actividad de copia en **DynamicsSink**. En la sección **sink** de la actividad de copia se admiten las siguientes propiedades.

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del receptor de la actividad de copia debe establecerse en: **DynamicsSink**. | Sí |
| writeBehavior | El comportamiento de escritura de la operación.<br/>El valor permitido es **"Upsert"**. | Sí |
| writeBatchSize | El recuento de filas de datos escritos en Dynamics en cada lote. | No (el valor predeterminado es 10) |
| ignoreNullValues | Indica si se omiten los valores nulos de los datos de entrada (excepto los campos de clave) durante la operación de escritura.<br/>Los valores permitidos son **true** y **false**.<br>- **True**: deja los datos del objeto de destino sin cambiar cuando realiza una operación upsert/update. Inserta un valor predeterminado definido al realizar una operación insert.<br/>- **False**: actualiza los datos del objeto de destino a NULL cuando realiza una operación upsert/update. Inserta un valor NULL al realizar una operación insert. | No (el valor predeterminado es false) |

>[!NOTE]
>El valor predeterminado del receptor writeBatchSize y el de la actividad de copia [parallelCopies](copy-activity-performance.md#parallel-copy) del receptor de Dynamics es 10 en ambos casos. Por lo tanto, se enviarán 100 registros a Dynamics simultáneamente.

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Asignación de tipos datos de Dynamics

Al copiar datos desde Dynamics, se utilizan las siguientes asignaciones de tipos de datos de Dynamics en los tipos de datos provisionales de Data Factory. Consulte el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para más información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen al receptor.

Configure el tipo de datos de Data Factory correspondiente en la estructura del conjunto de datos en función del tipo de datos de Dynamics de origen mediante la siguiente tabla de asignación.

| Tipo de datos de Dynamics | Tipo de datos provisionales de Data Factory | Se admite como origen | Se admite como receptor |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | boolean | ✓ | ✓ |
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | DECIMAL | ✓ | ✓ |
| AttributeType.Double | Doble | ✓ | ✓ |
| AttributeType.EntityName | string | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | |
| AttributeType.ManagedProperty | boolean | ✓ | |
| AttributeType.Memo | string | ✓ | ✓ |
| AttributeType.Money | DECIMAL | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | string | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |


> [!NOTE]
> No se admiten los tipos de datos de Dynamics AttributeType.CalendarRules y AttributeType.PartyList.

## <a name="next-steps"></a>Pasos siguientes
Para ver la lista de almacenes de datos que la actividad de copia de Data Factory admite como orígenes y receptores consulte [Almacenes de datos y formatos que se admiten](copy-activity-overview.md#supported-data-stores-and-formats).
