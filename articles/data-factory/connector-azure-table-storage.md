---
title: Copia de datos con Azure Table Storage como origen o destino mediante Azure Data Factory | Microsoft Docs
description: Aprenda a copiar datos desde almacenes de origen compatibles a Azure Table Storage o desde Table Storage a almacenes de receptor compatibles mediante Data Factory.
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
ms.openlocfilehash: fde85936760a167f1da2289ac1d18e97df7c9c04
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2018
---
# <a name="copy-data-to-and-from-azure-table-storage-by-using-azure-data-factory"></a>Copia de datos con Azure Table Storage como origen o destino mediante Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1: ya está disponible con carácter general](v1/data-factory-azure-table-connector.md)
> * [Versión 2: versión preliminar](connector-azure-table-storage.md)

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos con Azure Table Storage como origen o destino. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de esta actividad.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en versión preliminar. Si utiliza la versión 1 de Data Factory, que está disponible con carácter general, consulte el artículo sobre el [conector de Table Storage en la versión 1](v1/data-factory-azure-table-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos desde cualquier almacén de datos de origen admitido a Table Storage. También puede copiar datos de Table Storage en cualquier almacén de datos receptor admitido. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector de Azure Table Storage permite copiar datos utilizando las autenticaciones de clave de cuenta y firma de acceso compartido de servicio.

## <a name="get-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

En las secciones siguientes se proporciona información acerca de las propiedades que se usan para definir entidades de Data Factory específicas de Table Storage.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

### <a name="use-an-account-key"></a>Uso de una clave de cuenta

Puede crear un servicio vinculado de Azure Storage mediante la clave de cuenta. Esto proporciona a la factoría de datos acceso global a Storage. Se admiten las siguientes propiedades.

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type tiene que establecerse en **AzureStorage**. |Sí |
| connectionString | Especifique la información necesaria para conectarse a Storage para la propiedad connectionString. Marque este campo como SecureString. |Sí |
| connectVia | El [entorno de ejecución de integración](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) (si el almacén de datos se encuentra en una red privada) o Azure Integration Runtime. Si no se especifica, se usará Azure Integration Runtime. |Sin  |

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

### <a name="use-service-shared-access-signature-authentication"></a>Use la autenticación con firma de acceso compartido de servicio

También puede crear un servicio vinculado de Storage mediante una firma de acceso compartido. Esto proporciona a la factoría de datos acceso restringido o limitado por el tiempo a todos los recursos o a algunos específicos del almacenamiento.

Una firma de acceso compartido ofrece acceso delegado a recursos en la cuenta de almacenamiento. Puede utilizarla para conceder a un cliente permisos limitados para objetos en su cuenta de almacenamiento durante un período específico y con un conjunto determinado de permisos. No tiene que compartir las claves de acceso de su cuenta. La firma de acceso compartido es un URI que incluye en sus parámetros de consulta toda la información necesaria para el acceso autenticado a un recurso de almacenamiento. Para obtener acceso a los recursos de almacenamiento con la firma de acceso compartido, el cliente solo tiene que pasar la firma de acceso compartido al método o constructor adecuados. Para más información acerca de las firmas de acceso compartido, consulte el artículo de [información sobre el modelo de firmas de acceso compartido ](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!IMPORTANT]
> Data Factory ahora admite solo firmas de acceso compartido de servicio y no admite las firmas de acceso compartido de cuenta. Para más información acerca de estos dos tipos y cómo construirlos consulte [Tipos de firmas de acceso compartido](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures). El URL de firma de acceso compartido generado desde Azure Portal o Explorador de Azure Storage es una firma de acceso compartido de cuenta, por lo tanto no es compatible.

> [!TIP]
> Puede ejecutar los siguientes comandos de PowerShell para generar una firma de acceso compartido de servicio para la cuenta de almacenamiento. Reemplace los marcadores de posición y conceda el permiso necesario.
> `$context = New-AzureStorageContext -StorageAccountName <accountName> -StorageAccountKey <accountKey>`
> `New-AzureStorageContainerSASToken -Name <containerName> -Context $context -Permission rwdl -StartTime <startTime> -ExpiryTime <endTime> -FullUri`

Para usar la autenticación con firma de acceso compartido de servicio, se admiten las siguientes propiedades.

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type tiene que establecerse en **AzureStorage**. |Sí |
| sasUri | Especifique el URI de firma de acceso compartido a los recursos de Storage como blob, contenedor o tabla. Marque este campo como SecureString. |Sí |
| connectVia | El [entorno de ejecución de integración](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Azure Integration Runtime autohospedado (si el almacén de datos se encuentra en una red privada) o Azure Integration Runtime. Si no se especifica, se usará Azure Integration Runtime. |Sin  |

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

Cuando cree un URI de firma de acceso compartido, tenga en cuenta lo siguiente:

- Establezca los permisos de lectura y escritura adecuados en los objetos, en función de cómo se utilizará el servicio vinculado (lectura, escritura, lectura y escritura) en la factoría de datos.
- Establezca la **hora de expiración** adecuadamente. Asegúrese de que el acceso a los objetos de Storage no expirará durante el período activo de la canalización.
- El URI debe crearse en el nivel correcto de la tabla en función de la necesidad.

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de Azure Table.

Para copiar datos con Azure Table como origen o destino, establezca la propiedad type del conjunto de datos en **AzureTable**. Se admiten las siguientes propiedades.

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del conjunto de datos debe establecerse en **AzureTable**. |Sí |
| tableName |Nombre de la tabla en la instancia de base de datos de Table Storage a la que hace referencia el servicio vinculado. |Sí |

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

En los almacenes de datos sin esquemas como Azure Table, Data Factory deduce el esquema de una de las maneras siguientes:

* Si especifica la estructura de los datos mediante la propiedad **structure** en la definición del conjunto de datos, Data Factory respeta esta como la estructura del esquema. En este caso, si una fila no contiene un valor para una columna, se proporciona un valor nulo para ella.
* Si no especifica la estructura de los datos mediante la propiedad **structure** en la definición del conjunto de datos, Data Factory deduce el esquema usando la primera fila de los datos. En este caso, si la primera fila no contiene el esquema completo, algunas columnas se pierden en el resultado de la operación de copia.

Por lo tanto, para los orígenes de datos sin esquemas, lo mejor es especificar la estructura de los datos mediante la propiedad **structure**.

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admiten el receptor y el origen de Azure Table.

### <a name="azure-table-as-a-source-type"></a>Azure Table como tipo de origen

Si va a copiar datos desde Azure Table Storage, establezca el tipo de origen de la actividad de copia en **AzureTableSource**. En la sección **source** de la actividad de copia se admiten las siguientes propiedades.

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del origen de la actividad de copia tiene que establecerse en **AzureTableSource**. |Sí |
| AzureTableSourceQuery |Utilice la consulta personalizada de Table Storage para leer los datos. Vea los ejemplos en la sección siguiente. |Sin  |
| azureTableSourceIgnoreTableNotFound |Indica si se debe permitir la excepción de la tabla no existir.<br/>Los valores válidos son **True** y **False** (valor predeterminado). |Sin  |

### <a name="azuretablesourcequery-examples"></a>ejemplos de azureTableSourceQuery

Si la columna de Azure Table es de tipo datetime:

```json
"azureTableSourceQuery": "LastModifiedTime gt datetime'2017-10-01T00:00:00' and LastModifiedTime le datetime'2017-10-02T00:00:00'"
```

Si la columna de Azure Table es de tipo string:

```json
"azureTableSourceQuery": "LastModifiedTime ge '201710010000_0000' and LastModifiedTime le '201710010000_9999'"
```

Si usa el parámetro de canalización, convierta el valor de fecha y hora al formato apropiado de acuerdo con los ejemplos anteriores.

### <a name="azure-table-as-a-sink-type"></a>Azure Table como tipo de receptor

Para copiar datos a la Tabla de Azure, establezca el tipo de receptor de la actividad de copia en **AzureTableSink**. En la sección **sink** de la actividad de copia se admiten las siguientes propiedades.

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type del receptor de la actividad de copia debe establecerse en **AzureTableSink**. |Sí |
| azureTableDefaultPartitionKeyValue |Valor predeterminado de la clave de la partición que puede usar el receptor. |No  |
| azureTablePartitionKeyName |Especifique el nombre de la columna cuyos valores se usan como claves de partición. Si no se especifica, se utiliza "AzureTableDefaultPartitionKeyValue" como clave de la partición. |Sin  |
| azureTableRowKeyName |Especifique el nombre de la columna cuyos valores se usan como claves de fila. Si no se especifica, use un GUID para cada fila. |Sin  |
| azureTableInsertType |Modo de insertar datos en la tabla de Azure. Esta propiedad controla si los valores de las filas existentes en la tabla de salida con claves de partición y de fila coincidentes se van a reemplazar o a combinar. <br/><br/>Los valores permitidos son: **merge** (valor predeterminado) y**replace**. <br/><br> Esta configuración aplica en el nivel de fila, no en el nivel de tabla. Ninguna opción elimina filas de la tabla de salida que no existen en la entrada. Consulte los temas [Insert or Merge Entity](https://msdn.microsoft.com/library/azure/hh452241.aspx) (Insertar o combinar entidad) e [Insert or Replace Entity](https://msdn.microsoft.com/library/azure/hh452242.aspx) (Insertar o remplazar entidad) para más información acerca de cómo funcionan estas opciones (combinación y reemplazo). |No  |
| writeBatchSize |Inserta datos en Azure Table cuando se alcanza el valor de writeBatchSize o writeBatchTimeout.<br/>Los valores permitidos son: enteros (número de filas). |No (el valor predeterminado es 10 000) |
| writeBatchTimeout |Inserta datos en Azure Table cuando se alcanza el valor de writeBatchSize o writeBatchTimeout.<br/>Los valores permitidos son intervalos de tiempo. Un ejemplo es "00:20:00" (20 minutos). |No (el valor predeterminado es 90 segundos; el tiempo de expiración predeterminado del cliente de almacenamiento) |

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

Asigne una columna de origen a una de destino con la propiedad **"translator"** para poder usar la columna de destino como azureTablePartitionKeyName.

En el ejemplo siguiente, la columna de origen DivisionID se asigna a la columna de destino DivisionID:

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

El valor "DivisionID" se especifica como clave de partición.

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Asignación de tipos de datos de Azure Table Storage

Al copiar datos con Azure Table Storage como origen o destino, se utilizan las siguientes asignaciones de tipos de datos de Azure Table en los tipos de datos provisionales de Data Factory. Para más información acerca de la forma en que la actividad de copia asigna el tipo de datos y el esquema de origen al receptor, consulte el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md).

Al mover datos a y desde Azure Table, se usan las siguientes [asignaciones definidas por Azure Table](https://msdn.microsoft.com/library/azure/dd179338.aspx) desde tipos OData de Azure Table a tipos .NET y viceversa.

| Tipo de datos de Azure Table Storage | Tipo de datos provisionales de Data Factory | Detalles |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |Matriz de bytes de hasta 64 KB. |
| Edm.Boolean |booleano |Valor booleano. |
| Edm.DateTime |Datetime |Valor de 64 bits expresado como hora universal coordinada (UTC). El intervalo admitido de DateTime comienza a la medianoche del 1 de enero de 1601 D.C. (E.C.), UTC. El intervalo finaliza el 31 de diciembre de 9999. |
| Edm.Double |double |Valor de punto flotante de 64 bits. |
| Edm.Guid |Guid |Identificador único global de 128 bits. |
| Edm.Int32 |Int32 |Entero de 32 bits. |
| Edm.Int64 |Int64 |Entero de 64 bits. |
| Edm.String |string |Valor codificado mediante UTF-16. Los valores de cadena pueden tener hasta 64 KB. |

## <a name="next-steps"></a>pasos siguientes
Para ver la lista de almacenes de datos que la actividad de copia de Data Factory admite como orígenes y receptores consulte [Almacenes de datos y formatos que se admiten](copy-activity-overview.md#supported-data-stores-and-formats).
