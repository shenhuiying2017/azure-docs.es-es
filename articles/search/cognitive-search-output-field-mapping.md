---
title: Asignar campos de entrada enriquecidos de Cognitive Search a los campos de salida en los índices de Azure Search | Documentos de Microsoft
description: Extraer y enriquecer los campos de datos de origen y asignarlos a los campos de salida de un índice de Azure Search.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 67e4798070a73eebb8f61b0b260e3104e9ae6237
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786694"
---
# <a name="how-to-map-enriched-fields-to-a-searchable-index"></a>Cómo asignar campos enriquecidos a un índice de búsqueda

En este artículo, aprenderá a asignar campos de entrada enriquecidos a campos de salida en un índice de búsqueda. Una vez que haya [definido un conjunto de aptitudes](cognitive-search-defining-skillset.md), debe asignar los campos de salida de cualquier aptitud que aporte directamente valores a un campo dado del índice de búsqueda. Las asignaciones de campo son necesarias para mover contenido de documentos enriquecidos al índice.


## <a name="use-outputfieldmappings"></a>Usar outputFieldMappings
Para asignar los campos, agregue `outputFieldMappings` a la definición del indexador tal como se muestra a continuación:

```http
PUT https://[servicename].search.windows.net/indexers/[indexer name]?api-version=2017-11-11-Preview
api-key: [admin key]
Content-Type: application/json
```

El cuerpo de la solicitud está estructurado de la siguiente manera:

```json
{
    "name": "myIndexer",
    "dataSourceName": "myDataSource",
    "targetIndexName": "myIndex",
    "skillsetName": "myFirstSkillSet",
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction": {
                "name": "base64Encode"
            }
        }
    ],
    "outputFieldMappings": [
        {
            "sourceFieldName": "/document/content/organizations/*/description",
            "targetFieldName": "descriptions"
        },
        {
            "sourceFieldName": "/document/content/organizations",
            "targetFieldName": "orgNames"
        },
        {
            "sourceFieldName": "/document/content/sentiment",
            "targetFieldName": "sentiment"
        }
    ]
}
```
En cada asignación de campo de salida, establezca el nombre del campo enriquecido (sourceFieldName) y el nombre del campo, tal como se indica en el índice (targetFieldName).

La ruta de acceso en un campo sourceFieldName puede representar un elemento o varios elementos. En el ejemplo anterior, ```/document/content/sentiment``` representa un único valor numérico, mientras que ```/document/content/organizations/*/description``` representa varias descripciones de la organización. En casos donde hay varios elementos, estos son "reducidos" en una matriz que contiene cada uno de los elementos. Más concretamente, en el ejemplo ```/document/content/organizations/*/description```, los datos del campo *Descripciones* tienen el aspecto de una matriz plana de descripciones antes de que se indexe:

```
 ["Microsoft is a company in Seattle","LinkedIn's office is in San Francisco"]
```
## <a name="next-steps"></a>Pasos siguientes
Una vez haya asignado los campos enriquecidos a los campos de búsqueda, puede establecer los atributos de campo de cada uno de los campos de búsqueda [como parte de la definición del índice](search-what-is-an-index.md).

Para obtener información acerca de las asignaciones de campos, consulte [Field mappings in Azure Search indexers](search-indexer-field-mappings.md) (Asignaciones de campos en los indexadores de Azure Search).