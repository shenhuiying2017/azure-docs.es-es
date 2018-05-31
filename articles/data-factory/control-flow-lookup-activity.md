---
title: Actividad de búsqueda en Azure Data Factory | Microsoft Docs
description: Más información sobre cómo usar la actividad de búsqueda para buscar un valor desde un origen externo. Además, las actividades posteriores pueden hacer referencia a esta salida.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: shlo
ms.openlocfilehash: b6c2e2b685855455550612abb58ada6a694bbdff
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "34011533"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Actividad de búsqueda en Azure Data Factory

La actividad de búsqueda se puede usar para recuperar un conjunto de datos de cualquier parte del origen de datos compatible con ADF.  Se puede usar en el siguiente caso:
- Determinar dinámicamente en qué objetos (archivos, tablas, etc.) operar en una actividad posterior, en lugar de codificar de forma rígida el nombre del objeto

La actividad de búsqueda puede leer y devolver el contenido de un archivo de configuración, una tabla de configuración o el resultado de la ejecución de una consulta o procedimiento almacenado.  El resultado de la actividad de búsqueda se puede usar en una actividad de transformación o copia posterior si es un valor singleton, o bien en una actividad ForEach si es una matriz de atributos.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Azure Data Factory, que actualmente se encuentra en versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea la [documentación de Data Factory versión 1](v1/data-factory-introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Se admiten los siguientes orígenes de datos para la búsqueda. El número máximo de filas que puede devolver la actividad de búsqueda es **5000**, con un tamaño máximo de **2 MB**. Actualmente, además, la duración máxima de la actividad de búsqueda antes del tiempo de expiración es una hora.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Sintaxis

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>Propiedades de tipo
NOMBRE | DESCRIPCIÓN | Escriba | ¿Necesario?
---- | ----------- | ---- | --------
dataset | Proporciona la referencia de conjunto de datos para la búsqueda. Obtenga los detalles de la sección "Propiedades del conjunto de datos" de cada artículo del conector correspondiente. | Par clave-valor | Sí
de origen | Contiene propiedades de origen específicas para el conjunto de datos, al igual que el origen de la actividad de copia. Obtener los detalles de la sección "Copiar propiedades de la actividad" de cada artículo del conector correspondiente. | Par clave-valor | Sí
firstRowOnly | Indica si se deben devolver todas las filas o solo la primera. | boolean | Nº El valor predeterminado es `true`.

**Tenga en cuenta los siguientes puntos:**

1. No se admite la columna de origen con el tipo ByteArray.
2. La estructura no se admite en la definición del conjunto de datos. En el caso concreto de los archivos de formato de texto, puede utilizar la fila de encabezado para proporcionar el nombre de columna.
3. Si su origen de búsqueda es un archivo JSON y el valor `jsonPathDefinition` para volver a dar forma al objeto JSON no se admite, se recuperarán todos los objetos.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Utilizar el resultado de la actividad de búsqueda en una actividad posterior

El resultado de la búsqueda se devuelve en la sección `output` del resultado de ejecución de la actividad.

* **Cuando `firstRowOnly` está establecido en `true` (valor predeterminado)**, el formato de salida es como se muestra en el código siguiente. El resultado de búsqueda está en una clave `firstRow` fija. Para utilizar el resultado en una actividad posterior, utilice el patrón de `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Cuando `firstRowOnly` está establecido en `false`**, el formato de salida es como se muestra en el código siguiente. Un campo `count` indica el número de registros que se devuelven y se muestran los valores detallados en una matriz `value` fija. En tal caso, la actividad de búsqueda suele ir seguida de una [actividad ForEach](control-flow-for-each-activity.md). Puede pasar la matriz `value` al campo `items` de la actividad ForEach mediante el patrón de `@activity('MyLookupActivity').output.value`. Para acceder a elementos en la matriz `value`, use la siguiente sintaxis: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Este es un ejemplo: `@{activity('lookupActivity').output.value[0].tablename}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>Ejemplo
En este ejemplo, con la actividad de copia se copian datos de una tabla SQL en Azure SQL Database a Azure Blob Storage. El nombre de la tabla SQL se almacena en un archivo JSON en Blob Storage. La actividad de búsqueda busca el nombre de la tabla en entorno de tiempo de ejecución. Este enfoque permite modificar de forma dinámica el archivo JSON sin necesidad de volver a implementar canalizaciones ni conjuntos de datos. 

En este ejemplo se muestra solo la búsqueda de la primera fila. Para la búsqueda de todas las filas y encadenar los resultados con la actividad ForEach, consulte los ejemplos en [Copia de varias tablas en bloque mediante Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Canalización
Esta canalización contiene dos actividades: *búsqueda* y *copia*. 

- La actividad de búsqueda está configurada para usar LookupDataset, que hace referencia a una ubicación en una instancia de Azure Blob Storage. La actividad de búsqueda lee el nombre de la tabla SQL desde un archivo JSON en esta ubicación. 
- La actividad de copia usa la salida de la actividad de búsqueda (el nombre de la tabla SQL). La propiedad tableName en el conjunto de datos de origen (SourceDataset) se configura para usar la salida de la actividad de búsqueda. Con la actividad de copia se copian datos de la tabla SQL en una ubicación de Azure Blob Storage que especifica la propiedad SinkDataset. 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Conjunto de datos de búsqueda
El conjunto de datos de búsqueda hace referencia al archivo *sourcetable.json* de la carpeta de búsqueda en la instancia de Azure Storage que especifica el tipo AzureStorageLinkedService. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-the-copy-activity"></a>Conjunto de datos de origen para la actividad de copia
El conjunto de datos de origen usa la salida de la actividad de búsqueda, que es el nombre de la tabla SQL. Con la actividad de copia se copian los datos de esta tabla SQL en una ubicación de Azure Blob Storage que especifica el conjunto de datos del receptor. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>Conjunto de datos de receptor para la actividad de copia
Con la actividad de copia se copian datos de la tabla SQL en el archivo *filebylookup.csv* de la carpeta *csv* en Azure Storage que especifica la propiedad AzureStorageLinkedService. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Servicio vinculado de Azure Storage
Esta cuenta de almacenamiento contiene el archivo JSON con los nombres de las tablas SQL. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Servicio vinculado a Azure SQL Database
Esta instancia de Azure SQL Database contiene los datos que se copiarán en Blob Storage. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>Conjunto de objetos

```json
{
  "Id": "1",
  "tableName": "Table1",
}
{
   "Id": "2",
  "tableName": "Table2",
}
```

#### <a name="array-of-objects"></a>Matriz de objetos

```json
[ 
    {
        "Id": "1",
          "tableName": "Table1",
    }
    {
        "Id": "2",
        "tableName": "Table2",
    }
]
```

## <a name="next-steps"></a>Pasos siguientes
Consulte otras actividades de flujo de control compatibles con Data Factory: 

- [Actividad de ejecución de canalización](control-flow-execute-pipeline-activity.md)
- [Actividad ForEach](control-flow-for-each-activity.md)
- [Actividad de obtención de metadatos](control-flow-get-metadata-activity.md)
- [Actividad web](control-flow-web-activity.md)
