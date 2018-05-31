---
title: Aptitud de búsqueda cognitiva Combinación de texto (Azure Search) | Microsoft Docs
description: Combine el texto de una colección de campos en un solo campo consolidado. Use esta aptitud cognitiva en una canalización de enriquecimiento de Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: e288748d7433f4b3c7da7db1ab1ef2ee487318df
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
ms.locfileid: "33786744"
---
#    <a name="text-merge-cognitive-skill"></a>Aptitud cognitiva Combinación de texto

La aptitud **Combinación de texto** consolida el texto de una colección de campos en un solo campo. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.TextMerger

## <a name="skill-parameters"></a>Parámetros de la aptitud

Los parámetros distinguen mayúsculas de minúsculas.

| Nombre de parámetro     | DESCRIPCIÓN |
|--------------------|-------------|
| insertPreTag  | Cadena que se incluirá antes de cada inserción. El valor predeterminado es `" "`. Para omitir el espacio, establezca el valor en `""`.  |
| insertPostTag | Cadena que se incluirá después de cada inserción. El valor predeterminado es `" "`. Para omitir el espacio, establezca el valor en `""`.  |


##  <a name="sample-input"></a>Entrada de ejemplo
Un documento JSON con una entrada útil para esta aptitud podría ser:

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The brown fox jumps over the dog" ,
             "itemsToInsert": ["quick", "lazy"],
             "offsets": [3, 28],
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Salida de ejemplo
Este ejemplo muestra la salida de la entrada anterior, suponiendo que *insertPreTag* esté establecido en `" "` y *insertPostTag* esté establecido en `""`. 

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "mergedText": "The quick brown fox jumps over the lazy dog" 
           }
      }
    ]
}
```

## <a name="extended-sample-skillset-definition"></a>Definición del conjunto de aptitudes de ejemplo extendido

Un escenario común a la hora de utilizar Combinación de texto es combinar la representación textual de imágenes (el texto de una aptitud de OCR o la leyenda de una imagen) en el campo de contenido de un documento. 

El siguiente conjunto de aptitudes de ejemplo utiliza la aptitud OCR para extraer el texto de las imágenes insertadas en el documento. A continuación, crea un campo *merged_text* para que contenga el texto original y el texto de OCR de cada imagen. 

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "name": "OCR skill",
        "description": "Extract text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": "en",
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text"
          }
        ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Util.TextMerger",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " "
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetname" : "merged_text"
        }
      ]
    }
  ]
}
```
En el ejemplo anterior se asume que existe un campo de imágenes normalizadas. Para obtener este campo de imágenes normalizadas, establezca la configuración *imageAction* en la definición del indexador en *generateNormalizedImages*, tal como se muestra a continuación:

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "mergedText": "The quick brown fox jumps over the lazy dog" 
           }
      }
    ]
}
```

## <a name="see-also"></a>Otras referencias

+ [Aptitudes predefinidas](cognitive-search-predefined-skills.md)
+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)