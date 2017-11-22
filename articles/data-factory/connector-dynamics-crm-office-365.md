---
title: Copia de datos desde y hacia Dynamics CRM y 365 mediante Azure Data Factory | Microsoft Docs
description: "Obtenga información sobre cómo copiar datos desde Dynamics CRM y 365 a almacenes de datos receptores compatibles, o bien desde almacenes de datos de origen compatibles a Dynamics CRM y 365 a través de una actividad de copia de una canalización de Azure Data Factory."
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
ms.date: 11/09/2017
ms.author: jingwang
ms.openlocfilehash: c2de89ba3adaaa7d745731cff74269deecef03e2
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2017
---
# <a name="copy-data-fromto-dynamics-365dynamics-crm-using-azure-data-factory"></a>Copia de datos desde y hacia Dynamics CRM y 365 mediante Azure Data Factory

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos desde y hacia Dynamics CRM y 365. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Actividad de copia en V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar desde Dynamics 365 o CRM a cualquier almacén de datos receptor compatible, o copiar datos desde cualquier almacén de datos receptor compatible a Dynamics 365 o CRM. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector de Dynamics admite los siguientes tipos de autenticación y versiones de Dynamics:

| Versiones de Dynamics | Tipos de autenticación | Ejemplos de servicios vinculados |
|:--- |:--- |:--- |
| Dynamics 365 Online <br> Dynamics CRM Online | Office 365 | [Autenticación de Dynamics Online + Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 local con IFD <br> Dynamics CRM 2016 local con IFD <br> Dynamics CRM 2015 local con IFD | IFD | [Dynamics local con autenticación de IFD + IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

*IFD es la abreviatura en inglés del término "implementación con conexión a Internet".*

> [!NOTE]
> Para utilizar este conector de Dynamics, almacene la contraseña en Azure Key Vault y permita que la actividad de copia de ADF la extraiga de este lugar al realizar la copia de datos. Consulte la sección de configuración de la sección de [propiedades de servicios vinculados](#linked-service-properties).

## <a name="getting-started"></a>Introducción

Puede crear una canalización con la actividad de copia mediante el SDK de .NET, el SDK de Python, Azure PowerShell, la API de REST o la plantilla de Azure Resource Manager. Consulte el [tutorial de actividad de copia](quickstart-create-data-factory-dot-net.md) para obtener instrucciones paso a paso sobre cómo crear una canalización con una actividad de copia.

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas de Dynamics.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de Dynamics:

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 y Dynamics CRM Online

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en: **Dynamics**. | Sí |
| deploymentType | El tipo de implementación de la instancia de Dynamics. Debe ser **"Online"** para Dynamics Online. | Sí |
| organizationName | El nombre de la organización de la instancia de Dynamics. | No (se debe especificar cuando hay más de una instancia de Dynamics asociada al usuario) |
| authenticationType | Tipo de autenticación para conectarse a Dynamics. Especifique **"Office365"** para Dynamics Online. | Sí |
| nombre de usuario | Especifique el nombre de usuario para conectarse a Dynamics. | Sí |
| Contraseña | Especifique la contraseña de la cuenta de usuario especificada para el nombre de usuario. Tendrá que colocar la contraseña en Azure Key Vault y configurar la contraseña como "AzureKeyVaultSecret". Más información sobre [Almacenamiento de credenciales en Key Vault](store-credentials-in-key-vault.md). | Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Si no se especifica, se usará Azure Integration Runtime. | No para el origen, sí para el receptor |

>[!IMPORTANT]
>Para copiar datos en Dynamics, [cree un Azure IR](create-azure-integration-runtime.md#create-azure-ir) explícitamente con una ubicación próxima a Dynamics y asocie el servicio vinculado como en el ejemplo siguiente.

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
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 y Dynamics CRM local con IFD

*Las propiedades adicionales, en comparación con Dynamics Online, son "hostName" y "port".*

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en: **Dynamics**. | Sí |
| deploymentType | El tipo de implementación de la instancia de Dynamics. Debe ser **"OnPremisesWithIfd"** para Dynamic local con IFD.| Sí |
| **hostName** | El nombre de host del servidor local de Dynamics. | Sí |
| **port** | El puerto del servidor local de Dynamics. | No (el valor predeterminado es 443) |
| organizationName | El nombre de la organización de la instancia de Dynamics. | Sí |
| authenticationType | Tipo de autenticación para conectarse a Dynamics. Especifique **"Ifd"** para Dynamics local con IFD. | Sí |
| nombre de usuario | Especifique el nombre de usuario para conectarse a Dynamics. | Sí |
| Contraseña | Especifique la contraseña de la cuenta de usuario especificada para el nombre de usuario. Tenga en cuenta que tiene que colocar la contraseña en Azure Key Vault y configurar la contraseña como "AzureKeyVaultSecret". Más información sobre [Almacenamiento de credenciales en Key Vault](store-credentials-in-key-vault.md). | Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Si no se especifica, se usará Azure Integration Runtime. | No para el origen, sí para el receptor |

>[!IMPORTANT]
>Para copiar datos en Dynamics, [cree un Azure IR](create-azure-integration-runtime.md#create-azure-ir) explícitamente con la ubicación próxima a Dynamics y asocie el servicio vinculado como en el ejemplo siguiente.

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
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
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

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de Dynamics.

Para copiar datos desde y hacia Dynamics, establezca la propiedad type del conjunto de datos en **DynamicsEntity**. Se admiten las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos debe establecerse en: **DynamicsEntity**. |Sí |
| entityName | El nombre lógico de la entidad que se va a recuperar. | No para el origen (si se especifica "query" en el origen de la actividad); sí para el receptor |

> [!IMPORTANT]
>- **Al copiar datos desde Dynamics, la sección "structure" es necesaria**  en el conjunto de datos de Dynamics, que define el tipo de datos y el nombre de columna de los datos de Dynamics que quiere copiar. Obtenga más información en las secciones sobre [estructura del conjunto de datos](concepts-datasets-linked-services.md#dataset-structure) y [asignación de tipos de datos de Dynamics](#data-type-mapping-for-dynamics).
>- **Al copiar datos a Dynamics, la sección "structure" es opcional** en el conjunto de datos de Dynamics. El esquema de datos de origen determinará qué columnas se copian. Si el origen es un archivo CSV sin encabezado, en el conjunto de datos de entrada, especifique "structure" con el tipo de datos y el nombre de columna que se asigna a los campos del archivo CSV, uno por uno en orden.

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

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admite el origen y el receptor de Dynamics.

### <a name="dynamics-as-source"></a>Dynamics como origen

Para copiar datos de Dynamics, establezca el tipo de origen de la actividad de copia en **DynamicsSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en: **DynamicsSource**.  | Sí |
| query  | FetchXML es un lenguaje de consulta patentado que se usa en Microsoft Dynamics (Online y local). Consulte el ejemplo siguiente y obtenga más información en el artículo sobre cómo [generar consultas con FeachXML](https://msdn.microsoft.com/en-us/library/gg328332.aspx). | No (si se especifica "entityName" en el conjunto de datos)  |

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

### <a name="dynamics-as-sink"></a>Dynamics como receptor

Para copiar datos en Dynamics, establezca el tipo de receptor de la actividad de copia en **DynamicsSink**. Se admiten las siguientes propiedades en la sección **sink** de la actividad de copia:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del receptor de la actividad de copia debe establecerse en: **DynamicsSink**.  | Sí |
| writeBehavior | El comportamiento de escritura de la operación.<br/>El valor permitido es **"Upsert"**. | Sí |
| writeBatchSize | El recuento de filas de datos escritos en Dynamics en cada lote. | No (el valor predeterminado es 10) |
| ignoreNullValues | Indica si se omiten los valores nulos de los datos de entrada (excepto los campos de clave) durante la operación de escritura.<br/>Los valores permitidos son **true** y **false**.<br>- true: deje invariables los datos en el objeto de destino al realizar la operación de actualización/upsert, e inserte el valor predeterminado definido al realizar la operación de inserción.<br/>- false: actualice los datos en el objeto de destino a NULL al realizar la operación de actualización/upsert, e inserte el valor NULL al realizar la operación de inserción.  | No (el valor predeterminado es false) |

>[!NOTE]
>El valor predeterminado del receptor writeBatchSize y la actividad de copia [parallelCopies](copy-activity-performance.md#parallel-copy) del receptor de Dynamics son ambos 10, lo que significa que 100 registros se envían a Dynamics al mismo tiempo.

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

Al copiar datos desde Dynamics, se utilizan las siguientes asignaciones de tipos de datos de Dynamics en los tipos de datos provisionales de Azure Data Factory. Vea el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para obtener información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen en el receptor.

Configure el tipo de datos de ADF correspondiente en la estructura del conjunto de datos en función del tipo de datos de Dynamics de origen utilizando la siguiente tabla de asignación:

| Tipo de datos de Dynamics | Tipo de datos provisionales de Data Factory | Se admite como origen | Se admite como receptor |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | Booleano | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ |  |
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ |  |
| AttributeType.ManagedProperty | Booleano | ✓ |  |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ |  |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ |  |
| AttributeType.Status | Int32 | ✓ |  |


> [!NOTE]
> No se admiten los tipos de datos de Dynamics AttributeType.CalendarRules y AttributeType.PartyList.

## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.