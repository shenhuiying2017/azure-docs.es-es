---
title: Copia de datos con Azure Table Storage como origen o destino mediante Azure Data Factory | Microsoft Docs
description: "Obtenga información sobre cómo copiar datos desde almacenes de origen compatibles a Azure Table Storage o viceversa mediante Data Factory."
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
ms.date: 11/07/2017
ms.author: jingwang
ms.openlocfilehash: d7da4c3c4aa902cf9ccb97ebd13b7d16940f2c32
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2017
---
# <a name="copy-data-to-or-from-azure-table-using-azure-data-factory"></a>Copia de datos con Azure Table Storage como origen o destino mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versión 1: Disponibilidad general](v1/data-factory-azure-table-connector.md)
> * [Versión 2: Versión preliminar](connector-azure-table-storage.md)

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos con Azure Table Storage como origen o destino. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en la versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea el artículo sobre [la versión 1 del conector de Azure Table Storage](v1/data-factory-azure-table-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos desde cualquier almacén de datos de origen compatible a Azure Table Storage o viceversa. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector de Azure Table Storage permite copiar datos utilizando las autenticaciones de **clave de cuenta** y **SAS de servicio** (firma de acceso compartido).

## <a name="get-started"></a>Introducción
Puede crear una canalización con la actividad de copia mediante el SDK de .NET, el SDK de Python, Azure PowerShell, la API de REST o la plantilla de Azure Resource Manager. Consulte el [tutorial de actividad de copia](quickstart-create-data-factory-dot-net.md) para obtener instrucciones paso a paso sobre cómo crear una canalización con una actividad de copia.

En las secciones siguientes se proporciona información sobre las propiedades que se usan para definir entidades de Data Factory específicas de Azure Table Storage.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

### <a name="using-account-key"></a>Uso de la clave de cuenta

Puede crear un servicio vinculado de Azure Storage con la clave de cuenta, que proporciona a la factoría de datos acceso global a Azure Storage. Se admiten las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en: **AzureStorage** |Sí |
| connectionString | Especifique la información necesaria para conectarse a Almacenamiento de Azure para la propiedad connectionString. Marque este campo como SecureString. |Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) (si el almacén de datos se encuentra en una red privada) o Azure Integration Runtime. Si no se especifica, se usará Azure Integration Runtime. |No |

**Ejemplo:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-service-sas-authentication"></a>Uso de la autenticación de SAS de servicio

También puede crear un servicio vinculado de Azure Storage mediante una firma de acceso de compartido (SAS), que proporciona la factoría de datos acceso restringido o de tiempo limitado a todos los recursos, o a concretos, del almacenamiento.

Una Firma de acceso compartido (SAS) ofrece acceso delegado a los recursos en la cuenta de almacenamiento. Esto le permite conceder a un cliente permisos limitados a objetos en su cuenta de almacenamiento durante un período específico y con un conjunto determinado de permisos sin tener que compartir las claves de acceso a las cuentas. La SAS es un URI que incluye en sus parámetros de consulta toda la información necesaria para el acceso autenticado a un recurso de almacenamiento. Para obtener acceso a los recursos de almacenamiento con la SAS, el cliente solo tiene que pasar la SAS al método o constructor adecuados. Para obtener información detallada acerca de SAS, consulte [Firmas de acceso compartido: Descripción del modelo SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md)

> [!IMPORTANT]
> Ahora Azure Data Factory solo admite **SAS de servicio**, pero no SAS de cuenta. Consulte [Tipos de firmas de acceso compartido](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures) para más información acerca de estos dos tipos y cómo construirlos. La dirección URL de SAS generable desde Azure Portal o el Explorador de Storage es una SAS de cuenta, que no es compatible.
>

Para usar la autenticación de SAS de servicio, se admiten las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en: **AzureStorage** |Sí |
| sasUri | Especifique el URI de Firma de acceso compartido a los recursos de Azure Storage como blob, contenedor o tabla. Marque este campo como SecureString. |Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) (si el almacén de datos se encuentra en una red privada) o Azure Integration Runtime. Si no se especifica, se usará Azure Integration Runtime. |No |

**Ejemplo:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

Al crear un **URI de SAS**, tenga en cuenta lo siguiente:

- Establezca los **permisos** de lectura y escritura adecuados en los objetos, en función de cómo se utilizará el servicio vinculado (lectura, escritura, lectura y escritura) en la factoría de datos.
- Establezca la **hora de expiración** adecuadamente. Asegúrese de que el acceso a los objetos de Azure Storage no expirará durante el período activo de la canalización.
- El URI debe crearse en el nivel correcto de la tabla en función de la necesidad.

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre conjuntos de datos. En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de Azure Table.

Para copiar datos con Azure Table Storage como origen o destino, establezca la propiedad type del conjunto de datos en **AzureTable**. Se admiten las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos debe establecerse en: **AzureTable**. |Sí |
| tableName |Nombre de la tabla en la instancia de Base de datos de tablas de Azure a la que hace referencia el servicio vinculado. |Sí |

**Ejemplo:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Esquema de Data Factory

En los almacenes de datos sin esquemas como Tabla de Azure, el servicio Data Factory deduce el esquema de una de las maneras siguientes:

1. Si especifica la estructura de los datos mediante la propiedad **structure** en la definición del conjunto de datos, el servicio Data Factory respeta esta como la estructura del esquema. En este caso, si una fila no contiene un valor para una columna, se proporciona un valor nulo para ella.
2. Si no especifica la estructura de los datos mediante la propiedad **structure** en la definición del conjunto de datos, Data Factory deduce el esquema usando la primera fila de los datos. En este caso, si la primera fila no contiene el esquema completo, algunas columnas se pierden en el resultado de la operación de copia.

Por lo tanto, para los orígenes de datos sin esquemas, lo mejor es especificar la estructura de los datos mediante la propiedad **structure** .

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admiten el receptor y el origen de Azure Table Storage.

### <a name="azure-table-as-source"></a>Azure Table Storage como origen

Si va a copiar datos desde Azure Table Storage, establezca el tipo de origen de la actividad de copia en **AzureTableSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en: **AzureTableSource**. |Sí |
| AzureTableSourceQuery |Utilice la consulta personalizada de Azure Table Storage para leer los datos. Consulte los ejemplos en la sección siguiente. |No |
| azureTableSourceIgnoreTableNotFound |Indica si se omite la excepción de la tabla inexistente.<br/>Los valores válidos son **True** y **False** (valor predeterminado). |No |

### <a name="azuretablesourcequery-examples"></a>ejemplos de azureTableSourceQuery

Si la columna de la Tabla de Azure es de tipo cadena:

```json
"azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', <datetime parameter>)"
```

Si la columna de la Tabla de Azure es de tipo datetime:

```json
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', <datetime parameter>, <datetime parameter>)"
```

### <a name="azure-table-as-sink"></a>Azure Table Storage como receptor

Si va a copiar datos desde Azure Table Storage, establezca el tipo de origen de la actividad de copia en **AzureTableSink**. Se admiten las siguientes propiedades en la sección **sink** de la actividad de copia:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en: **AzureTableSink**. |Sí |
| azureTableDefaultPartitionKeyValue |Valor predeterminado de la clave de la partición que puede usar el receptor. |No |
| azureTablePartitionKeyName |Especifique el nombre de la columna cuyos valores se usan como claves de partición. Si no se especifica, se utiliza "AzureTableDefaultPartitionKeyValue" como clave de la partición. |No |
| azureTableRowKeyName |Especifique el nombre de la columna cuyos valores se usan como claves de fila. Si no se especifica, use un GUID para cada fila. |No |
| azureTableInsertType |Modo de insertar datos en la tabla de Azure. Esta propiedad controla si los valores de las filas existentes en la tabla de salida con claves de partición y de fila coincidentes se van a reemplazar o a combinar. <br/><br/>Los valores permitidos son: **merge** (valor predeterminado) y**replace**. <br/><br> Esta configuración se aplica en el nivel de fila, no en el nivel de tabla, y ninguna opción elimina filas de la tabla de salida que no existan en la entrada. Consulte los temas [Insert or Merge Entity](https://msdn.microsoft.com/library/azure/hh452241.aspx) (Insertar o combinar entidad) e [Insert or Replace Entity](https://msdn.microsoft.com/library/azure/hh452242.aspx) (Insertar o remplazar entidad) para más información sobre cómo funcionan estas opciones (combinación y reemplazo). |No |
| writeBatchSize |Inserta datos en la tabla de Azure cuando se alcanza el valor de writeBatchSize o writeBatchTimeout.<br/>Los valores permitidos son: enteros (número de filas). |No (el valor predeterminado es 10 000) |
| writeBatchTimeout |Inserta datos en la tabla de Azure cuando se alcanza el valor de writeBatchSize o writeBatchTimeout.<br/>Los valores permitidos son: intervalos de tiempo. Ejemplo: "00:20:00" (20 minutos). |No (el valor predeterminado es 90 segundos; el tiempo de expiración predeterminado del cliente de almacenamiento) |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

Asigne una columna de origen a una de destino con la propiedad "translator" para poder usar la columna de destino como azureTablePartitionKeyName.

En el ejemplo siguiente, la columna de origen DivisionID se asigna a la columna de destino DivisionID.

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

El valor DivisionID se especifica como clave de partición.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Asignación de tipos de datos de Azure Table Storage

Al copiar datos con Azure Table Storage como origen o destino, se utilizan las siguientes asignaciones de tipos de datos de Azure Table Storage en los tipos de datos provisionales de Azure Data Factory. Consulte el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen al receptor.

Al mover datos a y desde Azure Table, se usan las siguientes [asignaciones definidas por Azure Table service](https://msdn.microsoft.com/library/azure/dd179338.aspx) desde tipos OData de Azure Table a tipos .NET y viceversa.

| Tipo de datos de Azure Table Storage | Tipo de datos provisionales de Data Factory | Detalles |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |Matriz de bytes de hasta 64 KB. |
| Edm.Boolean |booleano |Valor booleano. |
| Edm.DateTime |DateTime |Valor de 64 bits expresado como hora universal coordinada (UTC). El intervalo admitido de DateTime comienza a las 12:00 de la noche del 1 de enero de 1601 D.C. (E.C.), UTC. El intervalo finaliza el 31 de diciembre de 9999. |
| Edm.Double |double |Valor de punto flotante de 64 bits. |
| Edm.Guid |Guid |Identificador único global de 128 bits. |
| Edm.Int32 |Int32 |Entero de 32 bits. |
| Edm.Int64 |Int64 |Entero de 64 bits. |
| Edm.String |String |Valor codificado mediante UTF-16. Los valores de cadena pueden tener hasta 64 KB. |

## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.