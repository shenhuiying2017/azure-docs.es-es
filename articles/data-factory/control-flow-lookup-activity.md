---
title: "Actividad de búsqueda en Azure Data Factory | Microsoft Docs"
description: "Obtenga información sobre cómo usar la actividad de búsqueda para buscar un valor desde un origen externo. Además, las actividades posteriores pueden hacer referencia a esta salida."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: shlo
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: spelluru
ms.openlocfilehash: 30173f8eea2ccbbcd44018596cf34b3769a64b50
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="lookup-activity-in-azure-data-factory"></a>Actividad de búsqueda en Azure Data Factory
La actividad de búsqueda puede usarse para leer o buscar un registro, un nombre de tabla o un valor de cualquier origen externo. Además, las actividades posteriores pueden hacer referencia a esta salida. 

Actualmente se admiten los siguientes orígenes de datos para la actividad de búsqueda:
- Archivo JSON en Azure Blob
- Archivo JSON local
- Azure SQL Database (datos JSON convertidos desde una consulta)
- Azure Table Storage (datos JSON convertidos desde una consulta)

La actividad de consulta resulta útil cuando se desea recuperar dinámicamente una lista de archivos, registros o tablas a partir de un archivo de configuración o un origen de datos. Además, otras actividades pueden hacer referencia a la salida de la actividad para llevar a cabo un procesamiento específico solo en esos elementos.

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en la versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea la [documentación de Data Factory V1](v1/data-factory-introduction.md).


## <a name="example"></a>Ejemplo
En este ejemplo, con la actividad de copia se copian datos de una tabla SQL en Azure SQL Database a Azure Blob Storage. El nombre de la tabla SQL se almacena en un archivo JSON en Blob Storage. La actividad de búsqueda busca el nombre de la tabla en entorno de tiempo de ejecución. Este enfoque permite modificar dinámicamente el archivo JSON sin volver a implementar canalizaciones ni conjuntos de datos. 

### <a name="pipeline"></a>Canalización
Esta canalización contiene dos actividades: **búsqueda** y **copia**. 

- La actividad de búsqueda está configurada para usar LookupDataset, que hace referencia a una ubicación en una instancia de Azure Blob Storage. La actividad de búsqueda lee el nombre de la tabla SQL desde un archivo JSON en esta ubicación. 
- La actividad de copia usa la salida de la actividad de búsqueda (el nombre de la tabla SQL). tableName en el conjunto de datos de origen (SourceDataset) se configura para usar la salida de la actividad de búsqueda. Con la actividad de copia se copian datos de la tabla SQL en una ubicación de Azure Blob Storage especificada por SinkDataset. 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
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
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.tableName}" 
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
El conjunto de datos de búsqueda hace referencia al archivo sourcetable.json de la carpeta de búsqueda en la instancia de Azure Storage especificada por AzureStorageLinkedService. 

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
El conjunto de datos de origen usa la salida de la actividad de búsqueda, que es el nombre de la tabla SQL. Con la actividad de copia se copian los datos de esta tabla SQL en una ubicación de Azure Blob Storage especificada por el conjunto de datos de receptor. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>Conjunto de datos de receptor para la actividad de copia
Con la actividad de copia se copian datos de la tabla SQL en el archivo filebylookup.csv de la carpeta csv en Azure Storage especificado por AzureStorageLinkedService. 

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

### <a name="azure-storage-linked-service"></a>Servicio vinculado de Almacenamiento de Azure
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



## <a name="type-properties"></a>Propiedades de tipo
Nombre | Descripción | Tipo | Obligatorio
---- | ----------- | ---- | --------
dataset | El atributo database se usa para proporciona la referencia de conjunto de datos para la búsqueda. Actualmente, los tipos de conjuntos de datos compatibles son:<ul><li>FileShareDataset</li><li>AzureBlobDataset</li><li>AzureSqlTableDataset</li><li>AzureTableDataset</li> | Par clave-valor | Sí
de origen | Propiedades de origen específicas para el conjunto de datos, al igual que el origen de la actividad de copia | Par clave-valor | No
firstRowOnly | Devuelve la primera fila o todas las filas. | boolean | No

## <a name="next-steps"></a>Pasos siguientes
Consulte otras actividades de flujo de control compatibles con Data Factory: 

- [Actividad de ejecución de canalización](control-flow-execute-pipeline-activity.md)
- [Para cada actividad](control-flow-for-each-activity.md)
- [Actividad de obtención de metadatos](control-flow-get-metadata-activity.md)
- [Actividad web](control-flow-web-activity.md)
