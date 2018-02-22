---
title: Copia de datos de Xero con Azure Data Factory (beta) | Microsoft Docs
description: "Obtenga información sobre cómo copiar datos desde Xero a almacenes de datos receptores compatibles a través de una actividad de copia de una canalización de Azure Data Factory."
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
ms.date: 02/12/2018
ms.author: jingwang
ms.openlocfilehash: 458ad702b510c0fd01ab63541b2026b8a9a06e91
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/13/2018
---
# <a name="copy-data-from-xero-using-azure-data-factory-beta"></a>Copiar datos de Xero con Azure Data Factory (beta)

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos de Xero. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, consulte [Actividad de copia en V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Este conector está actualmente en versión beta. Puede probarlo y enviarnos sus comentarios. No debe usarse en entornos de producción.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos de Xero en cualquier almacén de datos receptor admitido. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector Xero admite lo siguiente:

- [Aplicación privada](https://developer.xero.com/documentation/getting-started/api-application-types), pero no aplicación pública de Xero.
- Todas las tablas de Xero (puntos de conexión de API), excepto "Reports" (Informes). 

Azure Data Factory proporciona un controlador integrado para habilitar la conectividad. Por lo tanto, no es necesario instalar manualmente ningún controlador mediante este conector.

## <a name="getting-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector Xero.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de Xero:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en: **Xero**. | Sí |
| host | El punto de conexión del servidor de Xero (`api.xero.com`).  | Sí |
| consumerKey | Clave de consumidor asociada a la aplicación de Xero. Marque este campo como SecureString para almacenarlo de forma segura en Data Factory o [para hacer referencia a un secreto almacenado en Azure Key Vault](store-credentials-in-key-vault.md). | Sí |
| privateKey | La clave privada del archivo .pem que se generó para la aplicación privada de Xero; consulte [Create a public/private key pair](https://developer.xero.com/documentation/api-guides/create-publicprivate-key) (Creación de un par de claves pública y privada). Incluya todo el texto del archivo .pem, así como los finales de línea Unix (\n). Vea el ejemplo a continuación.<br/>Marque este campo como SecureString para almacenarlo de forma segura en Data Factory o [para hacer referencia a un secreto almacenado en Azure Key Vault](store-credentials-in-key-vault.md). | Sí |
| useEncryptedEndpoints | Especifica si los puntos de conexión de origen de datos se cifran mediante HTTPS. El valor predeterminado es true.  | Sin  |
| useHostVerification | Especifica si se requiere que el nombre de host del certificado del servidor coincida con el nombre de host del servidor al conectarse a través de SSL. El valor predeterminado es true.  | Sin  |
| usePeerVerification | Especifica si se debe verificar la identidad del servidor al conectarse a través de SSL. El valor predeterminado es true.  | Sin  |

**Ejemplo:**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "host" : "api.xero.com",
            "consumerKey": {
                 "type": "SecureString",
                 "value": "<consumerKey>"
            },
            "privateKey": {
                 "type": "SecureString",
                 "value": "<privateKey>"
            }
        }
    }
}
```

**Valor de clave privada de ejemplo:**

Incluya todo el texto del archivo .pem, así como los finales de línea Unix (\n).

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de Xero.

Para copiar datos de Xero, establezca la propiedad type del conjunto de datos en **XeroObject**. No hay ninguna propiedad específica de tipo adicional en este tipo de conjunto de datos.

**Ejemplo**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admite el origen de Xero.

### <a name="xero-as-source"></a>Xero como origen

Para copiar datos de Xero, establezca el tipo de origen de la actividad de copia en **XeroSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del origen de la actividad de copia debe establecerse en: **XeroSource** | Sí |
| query | Use la consulta SQL personalizada para leer los datos. Por ejemplo: `"SELECT * FROM Contacts"`. | Sí |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Tenga en cuenta lo siguiente al especificar la consulta de Xero:

- Las tablas con elementos complejos se dividirán en varias tablas. Por ejemplo, las transacciones bancarias tienen una estructura de datos compleja "LineItems", así que los datos de la transacción bancaria se asignan a las tablas `Bank_Transaction` y `Bank_Transaction_Line_Items`, con la clave externa `Bank_Transaction_ID` para vincularlas.

- Los datos de Xero están disponibles a través de dos esquemas: `Minimal` (predeterminado) y `Complete`. El esquema Complete contiene tablas de llamadas de requisitos previos que necesitan datos adicionales (por ejemplo, la columna ID) antes de realizar la consulta deseada.

Las siguientes tablas tienen la misma información en los esquemas Minimal y Complete. Para reducir el número de llamadas API, use un esquema Minimal (predeterminado).

- Bank_Transactions
- Contact_Groups 
- Contactos 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- Facturas 
- Invoices_Credit_Notes
- Invoices_ Prepayments 
- Invoices_Overpayments 
- Manual_Journals 
- Overpayments 
- Overpayments_Allocations 
- Prepayments 
- Prepayments_Allocations 
- Receipts 
- Receipt_Validation_Errors 
- Tracking_Categories

Las siguientes tablas solo se pueden consultar con el esquema Complete:

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Complete.Contacts_Contact_ Persons 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_ Payments 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="next-steps"></a>pasos siguientes
Para obtener una lista de los almacenes de datos que admite la actividad de copia, consulte los [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats).
