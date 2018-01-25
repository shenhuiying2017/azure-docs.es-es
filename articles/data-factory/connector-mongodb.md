---
title: Copia de datos desde MongoDB mediante Azure Data Factory | Microsoft Docs
description: "Obtenga información sobre cómo copiar datos desde MongoDB en almacenes de datos receptores compatibles a través de una actividad de copia de una canalización de Azure Data Factory."
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
ms.openlocfilehash: ddbd27bd832c6fc3c7a0274095d6d203ecf1092a
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Copia de datos desde MongoDB mediante Azure Data Factory de Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-on-premises-mongodb-connector.md)
> * [Versión 2: versión preliminar](connector-mongodb.md)

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos desde una base de datos MongoDB. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea el artículo sobre [la versión 1 del conector MongoDB](v1/data-factory-on-premises-mongodb-connector.md).


## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos desde la base de datos MongoDB en cualquier almacén de datos receptor compatible. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector MongoDB admite las siguientes funcionalidades:

- **Versiones 2.4, 2.6, 3.0 y 3.2** de MongoDB.
- Copiar datos mediante la autenticación **Básica** o **Anónima**.

## <a name="prerequisites"></a>requisitos previos

Para usar los datos de la copia de una base de datos MongoDB que no es accesible públicamente, debe configurar un entorno Integration Runtime (autohospedado). Consulte el artículo sobre el [entorno Integration Runtime (autohospedado)](create-self-hosted-integration-runtime.md) para obtener más información. El entorno Integration Runtime proporciona un controlador de MongoDB integrado, por lo tanto, no es necesario instalar manualmente los controladores cuando se copian datos desde MongoDB.

## <a name="getting-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Las secciones siguientes proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector MongoDB.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de MongoDB:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo |La propiedad type debe establecerse en: **MongoDb**. |Sí |
| Servidor |Dirección IP o nombre de host del servidor de MongoDB. |Sí |
| puerto |Puerto TCP que el servidor de MongoDB utiliza para escuchar las conexiones del cliente. |No (el valor predeterminado es 27017) |
| databaseName |Nombre de la base de datos de MongoDB a la que desea acceder. |Sí |
| authenticationType | Tipo de autenticación usado para conectarse a la base de datos MongoDB.<br/>Los valores permitidos son: **Básica** y **Anónima**. |Sí |
| Nombre de usuario |Cuenta de usuario para tener acceso a MongoDB. |Sí (si se usa la autenticación básica). |
| contraseña |Contraseña del usuario. Marque este campo como SecureString. |Sí (si se usa la autenticación básica). |
| authSource |Nombre de la base de datos de MongoDB que desea usar para comprobar las credenciales de autenticación. |Nº Para la autenticación básica, el valor predeterminado se utiliza la cuenta de administrador y la base de datos especificada mediante la propiedad databaseName. |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) o Azure Integration Runtime (si el almacén de datos es accesible públicamente). Si no se especifica, se usará Azure Integration Runtime. |Sin  |

**Ejemplo:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
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

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre conjuntos de datos. En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos MongoDB.

Para copiar datos desde MongoDB, establezca la propiedad type del conjunto de datos en **MongoDbCollection**. Se admiten las siguientes propiedades:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del conjunto de datos debe establecerse en: **MongoDbCollection**. | Sí |
| collectionName |Nombre de la colección en la base de datos de MongoDB. |Sí |

**Ejemplo:**

```json
{
     "name":  "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }

```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admite el origen MongoDB.

### <a name="mongodb-as-source"></a>MongoDB como origen

Para copiar datos desde MongoDB, establezca el tipo de origen de la actividad de copia en **MongoDbSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del origen de la actividad de copia debe establecerse en: **MongoDbSource**. | Sí |
| query |Utilice la consulta SQL-92 personalizada para leer los datos. Por ejemplo: select * from MyTable. |No (si se especifica "collectionName" en el conjunto de datos) |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> Cuando se especifica la consulta SQL, preste atención a la diferencia del formato de fecha y hora. Por ejemplo: `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', <datetime parameter>, <datetime parameter>)`

## <a name="schema-by-data-factory"></a>Esquema de Data Factory

El servicio de Azure Data Factory deduce el esquema de una colección de MongoDB mediante el **uso de los últimos 100 documentos de la colección**. Si estos 100 documentos no contienen el esquema completo, se pueden omitir algunas columnas durante la operación de copia.

## <a name="data-type-mapping-for-mongodb"></a>Asignación de tipos para MongoDB

Al copiar datos desde MongoDB, se utilizan las siguientes asignaciones de tipos de datos de MongoDB en los tipos de datos provisionales de Azure Data Factory. Consulte el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen al receptor.

| Tipo de datos de MongoDB | Tipo de datos provisionales de Data Factory |
|:--- |:--- |
| Binary |Byte[] |
| boolean |boolean |
| Date |Datetime |
| NumberDouble |Doble |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |string |
| string |string |
| UUID |Guid |
| Objeto |Renormalizado en columnas acopladas con "_" como separador anidado |

> [!NOTE]
> Para obtener más información sobre la compatibilidad con matrices con tablas virtuales, consulte la sección [Compatibilidad para tipos complejos que usan tablas virtuales](#support-for-complex-types-using-virtual-tables) que aparece más adelante.
>
> Actualmente, no se admiten los siguientes tipos de datos de MongoDB: DBPointer, JavaScript, Clave Max y Min, Expresión regular, Símbolo, Marca de tiempo y Sin definir.

## <a name="support-for-complex-types-using-virtual-tables"></a>Compatibilidad para tipos complejos que usan tablas virtuales

Azure Data Factory utiliza un controlador ODBC integrado para conectarse a una base de datos de MongoDB y copiar datos de dicha base de datos. Para los tipos complejos como matrices u objetos con diferentes tipos en los documentos, el controlador volverá a normalizar los datos en las tablas virtuales correspondientes. En concreto, si una tabla contiene estas columnas, el controlador generará las siguientes tablas virtuales:

* Una **tabla base**, que contiene los mismos datos que la tabla real, salvo las columnas de tipo complejo. La tabla base utiliza el mismo nombre que la tabla real a la que representa.
* Una **tabla virtual** para cada columna de tipo complejo, que ampliará los datos anidados. Para asignar un nombre a las tablas virtuales, se utiliza el nombre de la tabla real, un separador "_" y el nombre de la matriz u objeto.

Las tablas virtuales hacen referencia a los datos de la tabla real, lo que permite al controlador obtener acceso a los datos no normalizados. Para acceder al contenido de las matrices de MongoDB, puede crear consultas y combinar las tablas virtuales.

### <a name="example"></a>Ejemplo

Por ejemplo, la tabla "ExampleTable" que aparece a continuación es una tabla de MongoDB que tiene una columna con una matriz de objetos en cada celda: Facturas y una columna con una matriz de tipos escalares, Clasificaciones.

| _id | Nombre del cliente | Facturas | Nivel de servicios | Clasificaciones |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", item:"toaster", price:"456", discount:"0.2"}, {invoice_id:"124", item:"oven", price: "1235", discount: "0.2"}] |Silver |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", item:"fridge", price: "12543", discount: "0.0"}] |Gold |[1,2] |

El controlador generará varias tablas virtuales que representan a esta tabla. La primera tabla virtual es la tabla base y se denomina “ExampleTable”, tal y como se muestra en el ejemplo. La tabla base contiene todos los datos de la tabla original, pero los datos de las matrices se han omitido y se ampliarán en las tablas virtuales.

| _id | Nombre del cliente | Nivel de servicios |
| --- | --- | --- |
| 1111 |ABC |Silver |
| 2222 |XYZ |Gold |

Las siguientes tablas muestran las tablas virtuales que representan las matrices originales en el ejemplo. Estas tablas contienen lo siguiente:

* Una referencia a la columna de clave principal original correspondiente a la fila de la matriz original (a través del identificador de la columna)
* Una indicación de la posición de los datos dentro de la matriz original
* Los datos ampliados para cada elemento de la matriz

**Tabla "ExampleTable_Invoices":**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | Descuento |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |tostadora |456 |0,2 |
| 1111 |1 |124 |horno |1235 |0,2 |
| 2222 |0 |135 |frigorífico |12543 |0.0 |

**Tabla "ExampleTable_Ratings":**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |


## <a name="next-steps"></a>pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md##supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.