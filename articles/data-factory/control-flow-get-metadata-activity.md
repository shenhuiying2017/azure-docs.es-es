---
title: "Actividad de obtención de metadatos en Azure Data Factory | Microsoft Docs"
description: "Sepa cómo usar la actividad de procedimiento almacenado de SQL Server para invocar un procedimiento almacenado en una Base de datos SQL de Azure o en un Almacenamiento de datos SQL de Azure desde una canalización de Factoría de datos."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: shlo
ms.openlocfilehash: 99182b2ed91f6d60f499be0078077bf52fe8b366
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Actividad de obtención de metadatos en Azure Data Factory
La actividad GetMetadata se puede usar para recuperar metadatos de datos en Azure Data Factory. Esta actividad solo se admite para las factorías de datos de la versión 2. Se puede usar en los escenarios siguientes:

- Validación de la información de metadatos de datos
- Desencadenamiento de una canalización cuando los datos estén listos/disponibles.

La siguiente funcionalidad está disponible en el flujo de control:
- La salida de la actividad GetMetadata se puede usar en expresiones condicionales para llevar a cabo una validación.
- Se puede desencadenar una canalización si se satisface la condición mediante un bucle Do-Until

La actividad GetMetadata toma un conjunto de datos como entrada necesaria y genera información de metadatos disponible como salida. Actualmente solo se admite el conjunto de datos de Azure Blob Storage. Los campos de metadatos admitidos son el tamaño, la estructura y la hora lastModified.  

> [!NOTE]
> Este artículo se aplica a la versión 2 de Data Factory, que actualmente se encuentra en la versión preliminar. Si usa la versión 1 del servicio Data Factory, que está disponible con carácter general, vea la [documentación de Data Factory V1](v1/data-factory-introduction.md).


## <a name="syntax"></a>Sintaxis

### <a name="get-metadata-activity-definition"></a>Definición de la actividad de obtención de metadatos:
En el ejemplo siguiente, la actividad GetMetadata devuelve metadatos de los datos representados por MyDataset. 

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```
### <a name="dataset-definition"></a>Definición de conjunto de datos:

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "Filename": "file.json",
            "format":{
                "type":"JsonFormat"
                "nestedSeperator": ","
            }
        }
    }
}
```

### <a name="output"></a>Salida
```json
{
    "size": 1024,
    "structure": [
        {
            "name": "id",
            "type": "Int64"
        }, 
    ],
    "lastModified": "2016-07-12T00:00:00Z"
}
```

## <a name="type-properties"></a>Propiedades de tipo
Actualmente, la actividad GetMetadata puede capturar los siguientes tipos de información de metadatos de un conjunto de datos de Azure Storage.

Propiedad | Descripción | Valores permitidos | Obligatorio
-------- | ----------- | -------------- | --------
fieldList | Enumera los tipos de información de metadatos necesarios.  | <ul><li>size</li><li>structure</li><li>lastModified</li></ul> |    No<br/>Si está vacío, la actividad devuelve los tres tipos de información de metadatos admitidos. 
dataset | Conjunto de datos de referencia cuya actividad de metadatos debe recuperar la actividad GetMetadata. <br/><br/>El tipo de conjunto de datos admitido actualmente es Azure Blob. Las dos subpropiedades son: <ul><li><b>referenceName</b>: Referencia a un conjunto de datos existente de Azure Blob</li><li><b>type</b>: Puesto que se hace referencia al conjunto de datos, es del tipo "DatasetReference"</li></ul> |    <ul><li>String</li><li>DatasetReference</li></ul> | Sí

## <a name="next-steps"></a>Pasos siguientes
Consulte otras actividades de flujo de control compatibles con Data Factory: 

- [Actividad de ejecución de canalización](control-flow-execute-pipeline-activity.md)
- [Para cada actividad](control-flow-for-each-activity.md)
- [Actividad de búsqueda](control-flow-lookup-activity.md)
- [Actividad web](control-flow-web-activity.md)